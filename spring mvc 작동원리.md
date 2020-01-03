#### MVC

- Model : 도메인 모델
- View : 프레젠테이션을 표현
- Controller : Model과 View를 분리하기 위해 배치

#### Spring DispatcherServlet

- Servlet

  - 클라이언트 요청을 처리하고 그 결과를 다시 클라이언트에게 전송하는 `Servlet 클래스`의 구현 규칙을 지킨 Java Class

- Front Controller 역할

  - Web Application에 대한 요청들을 받고 그 요청을 Controller로 분배

- 스프링 IoC 컨테이너와 완전히 통합되어 스프링이 가진 모든 다른 기능을 사용할 수 있다.

- DispatcherServlet 등록 과정

  - Servlet Container가 실행

  - `ServletContainerInitializer`를 구현한 `SpringServletContainerInitializer`를 실행

  - `springServletContainerInitializer`는 `WebApplicationInitializer`를 implements한 class를 찾아내어 `onStartup` 메소드를 실행

    - ```java
      public class MyWebApplicationInitializer implements WebApplicationInitializer {
          @Override
          public void onStartup(ServletContext container) {
              ServletRegistration.Dynamic registration = container.addServlet("dispatcher", new DispatcherServlet());
              registration.setLoadOnStartup(1);
              registration.addMapping("/*");
          }
      }
      ```

  - 이 과정에서 `DispatcherServlet` 설정뿐만 아니라 `ContextLoaderListener`를 등록해야 한다

  - 최종적으로 2가지의 Context가 생성된다. (parent-child 관계)

    - `ContextLoaderListener`에 의해서 만들어지는 `Root WebApplicationContext` (parent)
      - 서비스계층과 DAO를 포함한 웹 환경에 독립적인 빈을 관리
    - `DispatcherServlet`에 의해서 만들어지는 `WebApplicationContext` (child)
      - `DispatcherServlet`이 직접 사용하는 컨트롤러를 포함한 웹 관련 빈을 등록하는데 사용
    - ![img](https://i.imgur.com/IUf4orm.png)

  #### 생명주기

  - `onStartup` 메소드로 넘어오는 `ServletContext`로 필터를 등록하고 적용
  - <img src="https://i.imgur.com/G8y0Pqa.jpg" alt="img" style="zoom: 80%;" />



#### Spring MVC 동작 순서

![img](https://i.imgur.com/eWjZX8j.png)

---

#### 참조

- https://minwan1.github.io/2018/05/28/2018-05-28-spring-mvc/