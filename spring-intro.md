

## Spring-PetClinic 프로젝트 분석을 통한 스프링 입문

### 인프런(예제로 배우는 스프링 입문) 강의 



### 프로젝트 셋팅

1. 메이븐 패키징(빌드): ./mvnw package : maven 관련 파일(의존성 파일)들이 다운로드 됨

2. jar파일로 프로젝트 실행
   - 방법1) java -jar tartget/*.jar: petclinic이라는 프로젝트가 jar로 배포되어 있으므로 실행시킴
   - 방법2) project main폴더의 petClinicApplication이라는 파일을 실행하는 방법

3. 디버깅 모드: src/resources/application.properties 파일에서 logging DEBUG 모드 설정 추가시: 기본로그보다 심화된 로그를 보면서 디버깅 할 수 있음

4. theme 설정: plugin -> marketplace -> material them ui  -> dark -> high contrast

### 디버깅 모드로 프로젝트 살펴보기

1. CMD+Shift+O: 파일/클래스 등 찾기
2. CMD+N: Generator (setter/getter 등)



### 에러 처리

1. org.apache.catalina.LifecycleException: Protocol handler start failed

- 포트 충돌 문제
- lsof -i:포트번호 -> PID 화인
- kill -9 PID -> 프로세스 제거
- 다시 실행 



### IOC(Inversion Of Controll)

- 제어의 역전
- 예를들어, 컨트롤러에서 레포 객체를 참조한다고 할때, 컨트롤로 클래스 내부에서 직접 인스터를 생성하는 방법이 아닌
- Spring IOC 컨테이너가 어노테이션 형식을 이용해, 필요한 레포 객체를 주입해주는 기능을 통해 DI하는 방법을 사용함

### DI(Dependency Injection)

- 의존성을 주입



### IOC Container

- ApplicationContext를 사용해서 구현(BeanFactory를 상속함) - BeanFactory보다 더 많은 기능을 구현함
- IOC Container의 DI는 스프링 컨테이너 안에 들어있는 Bean 끼리만 가능하다.
- IOC Container는 Bean을 싱글톤 스코프로 생성하여 재사용 가능하게 만든다.
- 싱글톤 스코프로 만들어 사용 및 관린하는 것은 번거로운 일인데, IOC Container가 대신 해준다.

### Bean

- IOC Container가 관리하는 객체
- 어떻게 등록하지?
  - 방법1) @Component Scan: 어디서 부터 컴포넌트들을 스캔할건지 설정함, 아래 어노테이션들을 탐색하여 자동으로 빈 주입
    - @Component
      - @Repository
      - @Service
      - @Controller
      - @Configuration
  - 방법2) 직접 일일히 XML에 작성
  - 방법3) 자바 설정 파일에 등록(요즘 추세) -> Java Config Class에 @Configuration 어노테이션과 @Bean 어노테이션으로 등록
  - 특이사항) Repository 같은 경우 어노테이션 없이, Repository 라는 인터페이스를 상속받으면 자동으로 JPA를 이용하여 빈 주입해줌(JPA에서 추가적으로 학습할것)
- 어떻게 꺼내쓰지?
  - 방법1) @Autowired 또는 @Inject
    - Spring Version 3 부터 생성자에서 주입받는 파라미터가 1개이고 그것이 빈이라면 자동으로 Autowired
    - 생성자, 필드, Setter에 붙여서 사용가능(하지만, 생성자에 붙이는 것 권장, 왜? 필수 인스턴스를 강제할 수 있음)
    - 다만, 순환참조 A->B, B->A를 필수 인스턴스로 갖는경우에 둘 다 생성자에서 Autowried하면 어느것도 생성되지 않기 떄문에, 이런 경우 발생시 필드(또는 Setter)를 이용하여 문제 해결 할 수 있음
  - 방법2) ApplicationContext에서 getBean()으로 직접 꺼내 사용
- 특징
  - 오로지 "Bean"들만 의존성 주입을 해준다.

### AOP(Aspect Oriented Programming)

- 흩어진 코드를 한 곳으로 모아
- 관심사 기준(관전 지향) 프로그래밍
- 공통된 코드는 한곳에 모으로, 중간에 다르게 실행되는 부분은 따로 코딩한다.
- stopWath를 통해서 메소드 앞뒤에 다 들어가는 경우를 생각해보면 쉽다.
- AOP 구현 방법
  - 방법1) AspectJ 활용, 컴파일 A.java —> (AOP) —> A.class  (컴파일 과정에서 조작)
  - 방법2) 바이트 코드 조작, A.java —> A.class —> (AOP) —> 메모리 (클래스가 로딩되는 시점에서 조작)
  - 방법3) [프록시 패턴](https://refactoring.guru/design-patterns/proxy) 을 이용한 (Spring AOP는 이 방법을 사용)

### 프록시 패턴

- Proxy라는 놈이, 들어온 요청의 앞/뒤에 새로운 작업을 추가하여 새로운 요청을 만들어 목적지에 전달시키는 역할을 함
- 새로운 기능이 추가 됬지만, 기존의 코드를 건드리지 않았다는 것이 중요한 점



### AOP 사용 방법

- @Around, @After, @Before 등의 어노테이션을 사용하여 Aspect를 주입시킬 수 있음(Proxy라고 생각하면 됨)



### PSA(Portable Service Abstraction)

- 잘 만든 인터페이스
- Spring은 다양한 Service Abstraction을 제공함
- Portable의 의미는 우리의 소스를 거의 그대로 사용하면서, 톰캣과 같은 기술스택을 원하는 스택으로 변경할 수 있음을 의미한다.
- 추상화를 통해 기술이 바뀌어도 코드를 견고하게 유지할 수 있는 방법을 제공

###PSA 예제1) 스프링 웹 MVC

- 대표적인 예로, Spring MVC를 사용하면, 서블릿에서 진행하던 작업을 하지않고도 Spring MVC가 제공하는 추상화 메소드를 사용하여 간편하게 웹 어플리케이션을 구축할 수 있음

- 현재는 Spring Web MVC를 사용하면, 요청이 들어올때마다 하나의 쓰레드가 받아서 멀티 쓰레드 형식으로 프로그래밍 되도록 구성되어 있다.

- 하지만, Spring 5 부터 새롭게 도입된 webFlux를 사용하면 Tomcat대신 Netty 기반으로 웹 서버가 실행되며, 최소한의 스레드를 사용하여 프로그래밍 되도록 구서오디어 있다.

### PSA 예제2) 스프링 트랜잭션

- Platform Transaction Manager
- Or or Nothing 속성
- @Transactional 어노테이션을 통해서, setAutoCommit(0), try, catch, Rollback 등의 로직을 추가하지 않고, 알아서 해당 메소드는 자동으로 위 처리가 진행된다.

### PSA 예제3) 스프링 캐시



