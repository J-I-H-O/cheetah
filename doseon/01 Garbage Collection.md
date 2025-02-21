# 가비지 컬렉션이란

가비지 컬렉션은 힙 메모리를 살펴보고 사용 중인 객체와 사용하지 않는 객체를 식별한 후 사용하지 않는 객체를 삭제하는 프로세스이다.

사용중이라는 의미는 해당 객체에 대한 포인터가 여전히 유지 중이라는 뜻이다.

가비지 컬렉션 수집 방식의 기초를 먼저 설명하겠다,

# 가비지 컬렉션 과정

## 1. Marking

마킹은 사용중인 객체와 사용중이지않는 객체를 구분하는 작업입니다.
![image](https://github.com/user-attachments/assets/63c4cbb6-8793-4e6b-848a-6f22da9563e2)

## 2. Normal Deletion

일반 삭제는 참조되지 않는 객체를 삭제하는 과정입니다.
![image](https://github.com/user-attachments/assets/1212a6e3-b9d5-4b52-8cf8-ad554f3078c9)


## 2a. Deletion with Compacting

성능을 더욱 개선하려면 참조되지 않는 객체를 삭제하는 것 외에도 나머지 참조된 객체를 압축할 수도 있습니다.

참조된 객체를 함께 이동하면 새 메모리를 훨씬 쉽고 빠르게 할당가능합니다.
![image](https://github.com/user-attachments/assets/9b2b4c54-bfba-4b76-8232-948a2e4e2493)


위와 같은 과정들은 비효율적이다.

왜냐하면, 한번에 모든 객체들을 마킹하고 삭제하는 과정은 객체가 늘어남에 따라 수행시간이 길어진다.

그리고 어플리케이션의 대부분의 객체는 수명이 짧다.

![image](https://github.com/user-attachments/assets/0a2a80e3-44ae-4c2c-a373-256e1cfe1ff3)


Y축은 할당된 바이트 수를 나타내고 X 액세스는 시간 경과에 따라 할당된 바이트 수를 나타냅니다.

# JVM Generations

위와 같은 비효율적인 방식을 고치고자 JVM은 힙을 `Young Generation`, `Old Generation` , `Permanent Generation` 세가지의 세대로 나누었다.

![image](https://github.com/user-attachments/assets/d6c1739b-684a-40df-a193-7ee079b3c88c)

## Young Generation

Young Generation은 새 객체가 할당되고 유지되는 영역이다. 해당 영역이 가득 차면 `Minor Garbage Collection`이 발생한다. 

살아남은 객체는 나이들고 결국 OldGeneration으로 이동된다.

## Old Generation

Old Generation은 오래 살아남은 객체를 저장하는 데 사용된다. 일반적으로 Young Generation 객체의 나이 임계값이 있으며 임계값에 도달하면 Old로 이동하게 된다. 결국 Old Generation도 가득차면 Collection이 되어야하는데, 이 이벤트를 `Major Garbage Collection` 이라고 한다.

## Permanent Generation

애플리케이션에서 사용되는 클래스 및 메서드를 설명하는 데 필요한 메타데이터가 포함되어 있다.

Heap영역으로 구분되어있지만, 인터넷에서도 여러 의견차이가 많다. 주 메모리힙은 Young과 Old로 이루어져있으며, PermanentGenration은 특수한 힙이라고 하기도 한다. 확실한 건 주 된 개념의 힙과는 다른 분류로 보는 것이 맞는 것 같다.

Permanent Generation의 설명은 Static(Method) 영역과 비슷한데, 실제로 Static(Method) 영역은  JVM벤더마다 다르게 구현되어 있으며, JDK8이전 버전에서는 Permanent Generation으로 되어있으며, 이 JDK8부터는 이 영역은 MetaSpace로 대체되었다. MetaSpace는 PermanentGeneration과 달리 native memory를 사용한다.

## Stop the World Event

Minor Garbage Collection과 Major Garbage Collection 모두 Stop The World Event가 발생한다.

Minor에서 일어나는 이벤트는 무시해도 될 정도로 작기 때문에, Major에서만 Stop the World Event가 발생한다고 말하기도 한다.

Stop the World Event는 Garbage Collection의 수행이 끝나기 전까지 어플리케이션의 다른 스레드가 모두 정지되는 이벤트를 뜻한다.

Major Garbage Collection은 수집해야 할 영역이 young generation보다 크기 때문에 속도가 훨씬 느려지는 경우가 많다.

따라서 Major Garbage Collection은 최소화 해야한다.

# 메타인지 인터뷰 질문내용

## 1. Minor GC의 동작 과정에 대한 추가 설명
Young Generation은 eden, survivor0, survivor1 세가지 영역으로 나뉜다.
![image](https://github.com/user-attachments/assets/8500bc1f-08c8-4abe-8d7b-49415cf3ebaa)

객체가 할당되는 곳은 eden이다.

![image](https://github.com/user-attachments/assets/271d30ea-90d5-4bca-ae91-addfff8ba371)

이후, eden이 가득차게 되면 Minor GC가 수행된다.

![image](https://github.com/user-attachments/assets/ca942a63-0fa7-461f-8444-1d53f5712022)

마킹과정을 거치고, 살아남은 객체는 survivor영역 중 하나를 선택해 들어가게 된다.
이 때, 선택되는 survivor영역은 Minor GC발동 때마다, 서로 바뀌게 된다.

![image](https://github.com/user-attachments/assets/e3dd82d7-790d-43d6-8ae5-4c5a873cc79d)

다음 GC가 발동될 때는, aging과정이 들어간다. aging과정이란, survivor영역에서 마킹했을 때, 여전히 참조되고 있는 개객체들의 나이를 1씩 증가하는 과정이다.

![image](https://github.com/user-attachments/assets/c7442c0a-70db-4f8e-a920-a5b7366e53ea)

MinorGC가 발동될 때는 항상 eden영역과 survivor영역 한 곳이 같이 발동된다고 생각하면된다.

survivor0가 사용중이였다면, survivor0 에서 살아남은 객체는 survivor1로 이동하고, eden역시 survivor1로 이동시킨다.

즉, survivor영역은 하나의 영역만 사용된다.

## 2. Stop The World 발생 시 GC를 실행하는 스레드 이외의 모든 스레드가 정지된다고하는데, 그렇다면 GC가 발생하는 동안 들어온 요청은 처리하지못하고 유실되는가?
(나의 생각) 유실되지는 않고, 스레드풀의 accept-count에 의해 할당된 작업큐에 저장되고 있지않을까 싶다.

왜냐하면, accept count의 큐는 Max connection과 달리 OS공간에서 관리되는 큐이다. 그렇기 때문에 어플리케이션의 GC에 영향을 받지않고 사용자요청을 받아서 기다리게 할 수 있을 것 같다.

(테스트) accept count를 0으로하고 의도적으로 GC를 수행시키면 어떻게 될까?

## 3. Major GC를 최소화하기 위해서는 어떤 부분을 튜닝해야하는가?
1. Old Generation 크기를 늘리면 발생 빈도를 줄일 수 있다. 하지만, Stop The World Event의 시간이 더 증가할 가능성이 크다.
2. 애플리케이션의 특성에 따라 적합한 GC 알고리즘을 선택하면 Major GC로 인한 STW 시간을 줄일 수 있다.
* Throughput 중심:
  * Parallel GC (Throughput GC): GC 성능을 극대화하고 처리량을 높이는 데 적합.
* Low Latency 중심:
  * G1 GC: Major GC를 여러 작은 단위의 GC로 나누어 STW 시간을 줄이는 GC.
  * ZGC: 지연 시간을 최소화하고 초대형 힙을 지원.
  * Shenandoah GC: 낮은 지연 시간과 짧은 STW를 목표로 함.
3. 어플리케이션 레벨에서 객체를 현명하게 사용한다. 예를 들면, 래퍼타입보다는 원시타입을 사용. 공유자원을 사용. 등등. 


## 4. 메모리 공간을 압축하면 왜 더 쉽고 빠르게 할당이 가능해지는가?
메모리 공간을 압축한다는 것은, 사용중인 객체들 사이에 빈공간이 생기지 않도록 앞쪽으로 모두 땡기는 것이다.

이후 새로운 객체 할당 시, 제일 끝 객체기준으로 메모리를 할당하면 되기 때문에 새로운 객체 할당이 쉽고 빠르다.
