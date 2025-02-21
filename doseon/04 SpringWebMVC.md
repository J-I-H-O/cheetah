# SpringMVC 등장 배경

### 1. Servlet과 JSP의 한계

- Servlet은 비즈니스로직에 뷰로직이 침투하는 문제가 있음
- JSP는 복잡한 비즈니스로직을 처리하기 어려운 문제가 있음

### 2. structs 프레임워크의 한계

- Servlet과 JSP의 한계를 극복하고자 MVC패턴을 도입해 뷰로직과 비즈니스로직을 분리한 프레임워크
- 설정이 부족하고 유연성이 부족했음

### 3. Spring의 등장

- EJB의 복잡성과 부족한 유연성을 해결하기 위해 나옴
- EJB는 지켜야하는 스펙도 많아 기술 침투적이다. 비즈니스로직에 집중하기 힘들다.
- **Spring의 큰 장점은 비즈니스로직에 집중하기 좋다는 것**

### 4. SpringWebMVC(SpringMVC)

- Spring에서 웹개발을 돕기위해 나온 모듈

# SpringMVC은 어떻게 요청을 Mapping하는가

![springMVC](https://github.com/user-attachments/assets/c99ebf6e-6f3b-4c04-ad8d-370212779004)


## 1. DispactherServlet

- 사용자 요청을 받고 HandlerMaaping을 통해 해당 요청을 처리할 수 있는 적절한 Handler를 찾는다.
- 여기서 Handler란 요청을 처리할 수 있는 무언가를 뜻한다. Handler는 여러가지가 될 수 있는데, HandlerMethod, Servlet, HttpRequestHandler 처럼 여러 구현체가 있다.

## 2. HandlerMapping

- HandlerMapping은 Handler를 찾아주기 위한 인터페이스이다.
- 구현체로는 @RequestMapping을 기반으로 매핑해주는 RequestMappingHandlerMapping, bean의 이름을 기반으로 매핑해주는 BeanNameUrlHandlerMapping 등 여러가지가 있다.
- 최신 프로젝트에서 제일 많이 사용되는 것은 RequestMappingHandlerMapping이며 RequestMapping을 찾을 때, 1순위 구현체이다.

## 3. HandlerAdapter

- HandlerAdapter는 Handler를 실행하기 위한 인터페이스이다.
- 구현체로는 여러가지가 있지만, HandlerMapping과 마찬가지로 RequestMappingHandlerAdapter가 가장 많이 사용되며 1순위이다.

# DispatcherServlet의 초기화(feat. ApplicationContext)

DispatcherServlet은 어플리케이션 실행시점에 초기화작업을 한다.

초기화 과정에서 HandlerMapping과 HandlerAdapter또한 미리 초기화한다.

## HandlerMapping초기화

RequestMapping을 기준으로 설명한다.

HandlerMapping을 초기화 하기 위해서는 사용자 요청과 HandlerMethod이 1대1로 매핑되어야 한다.

HandlerMethod는 사용자 요청을 처리하는 method이다.(ex. Controller안의 메서드)

HandlerMethod가 나중에 Adapter에 의해 실행될 때, 리플렉션을 활용해 invoke()하여 실행된다.

리플렉션을 활용해 실행이 되려면 해당 메서드를 가진 객체가 필요한데, HandlerMethod는 미리 해당 객체를 멤버로 들고있다.

그래서 HandlerMapping초기화 시점에, ApplicationContext를 활용하여 요청에 맞는 적절한 bean(controller)를 찾아 적절한 method를 HandlerMethod로 변환하여 매핑한다.

## ApplicationContext 초기화

@Component, @bean을 기반으로 componentScan에 의해 클래스를 빈으로 등록하며 의존성 주입을 한다.

### ApplicationContext는 어플리케이션 실행중(운영단계)에는 사용안되나?

대부분의 bean은 singlethon scope로 어플리케이션 실행시점에 빈으로 등록되었고, 이미 DispatcherServlet초기화 시점에 역할을 다했기에 어플리케이션 실행 중에는 찾을 일이 없다.

하지만 다른 scope에서는 실행중에 ApplicationContext를 활용해 bean을 관리하게 될 수 있다.
