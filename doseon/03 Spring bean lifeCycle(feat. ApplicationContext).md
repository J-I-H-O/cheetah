# 1.스프링 빈이란

스프링 컨테이너에 의해 관리되는 재사용가능한 소프트웨어 컴포넌트를 뜻한다.

**스프링 컨테이너에서 관리하는 재사용가능한 자바 객체**

# 2. BeanFactory vs ApplicationContext

두 개념 모두 빈을 관리하는 스프링 컨테이너의 스펙을 다루는 인터페이스이다.

## 2.1 BeanFactory

스프링 컨테이너 스펙을 다루는 최상위 인터페이스

기본적으로 **빈을 지연초기화 하는 방법**을 선택하고있음

## 2.2 ApplicationContext

BeanFactory를 확장한 스프링컨테이너 인터페이스

기본적으로 **빈을 즉시초기화 하는 방법**을 선택하고 있음

# 3. 빈 생명주기

스프링 컨테이너 생성 -> Bean 생성 -> 의존성 주입 -> **초기화 콜백** -> Bean 사용 -> **소멸 전 콜백** -> 스프링 종료

### 3.1 빈 생성

@Bean 또는 @Component어노테이션 기반으로 빈을 생성한다

### 3.2 의존성 주입

해당 빈객체의 필드들에 대해서 의존성을 주입한다

하지만 **생성자 주입을 사용하는 빈의 경우 의존성 주입과 빈 생성을 동시**에 한다.

**필드 주입 또는 Setter주입의 경우 빈 생성 후 의존성 주입**한다.

### 3.3 초기화 콜백

객체를 생성하는 이외의 객체 초기화 시 로직을 수행한다.

@PostConstruct를 통해 가능하다.

### 3.4 소멸 전 콜백

어플리케이션 종료 시 호출 되는 부분이다.

# 4. 스프링 컨테이너(ApplicationContext)는 언제 생성될까?

![SpringContainer 초기화](https://github.com/user-attachments/assets/7baa8d34-dd7c-4a68-9814-a7477350644f)
Spirng어플리케이션 실행시 흔히 볼 수 있는 로그들이다.

그중에서 ServletWebServerApplicationContext를 초기화하는 로그가 보인다.

JPA Repository 스캔 및 초기화 → 톰캣설정 → <b> ApplicationContext초기화(eager bean 초기화) </b> →DB관련 설정→ …
