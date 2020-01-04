# spring boot

#### 의존성 관리 이해

1. parent POM 사용
   - 프로젝트의 pom.xml파일의  `spring-boot-starter-parent` -> `spring-boot-dependencies` 에서 관리

2. `<dependencyManagement>`를 사용하여 `spring-boot-dependencies`를 <artifactId>를 설정한다.
   - 단점 : 모든 버전을 설정해야하는 귀찮음



#### 의존성 관리 응용

- `springframework.boot`에 버전이 등록되어 있으면 <dependency> 추가할 때 버전을 추가하지 않아도 된다.

- springframework.boot의 버전을 바꿀때에는 프로젝트의 pom.xml 파일에 <properties>를 추가하여 변경한다.

  - ```xml
    <properties>
    	<spring.version>2.2.0.RELEASE</spring.version>
    	<java.version>1.8</java.version>
    </properties>
    ```



#### 자동 설정 이해

- `@SpringBootApplication` 이 포함하는 어노테이션
  -  `@EnableAutoConfiguration`
    - `@EnableAutoConfiguration`을 주석처리하면 웹 관련 bean이 등록되지 않아서 web application으로 되지 않는다.
    - 아니면 `application.properties` 파일에 `spring.main.web-application-type=none` 추가
  - `@ComponentScan`
  - `@Configuration`
    - `@SpringBootApplication`의 하위 클래스들의 어노테이션을 확인하여 bean으로 등록
    - 하위 클래스에 있지 않으면 bean으로 등록되지 않는다.
- 스프링은 빈을 2가지 방법으로 등록한다.
  - `@EnableAutoConfiguration`
    - `@ComponentScan`  이후에 bean을 등록
    - `spring-boot-actuator-autoconfigure\2.2.2.RELEASE\spring-boot-actuator-autoconfigure-2.2.2.RELEASE.jar!\META-INF\spring.factories` 의 설정파일을 적용한다.
    - `@ConditionalOn~~~` 등의 어노테이션 설정을 통해 bean으로 등록
  - `@ComponentScan`
    - `@Configuration @Repository @Service @Controller @RestController`



#### Custom AutoConfiguration 1부

- `xxx-spring-boot-starter` 프로젝트 생성

- ```xml
  <dependencies>
  	<dependency>
  		<groupId>org.springframework.boot</groupId>
  		<artifactId>spring-boot-autoconfigure</artifactId>
  	</dependency>
  	<dependency>
  		<groupId>org.springframework.boot</groupId>
  		<artifactId>spring-boot-autoconfigure-processor</artifactId>
  		<optional>true</optional>
  	</dependency>
  </dependencies>
  
  <dependencyManagement>
  	<dependencies>
  		<dependency>
  			<groupId>org.springframework.boot</groupId>
  			<artifactId>spring-boot-dependencies</artifactId>
  			<version>2.0.3.RELEASE</version>
  			<type>pom</type>
  			<scope>import</scope>
  		</dependency>
  	</dependencies>
  </dependencyManagement>
  ```

- @Configuration 파일 작성 

- src/main/resource/META-INF에 spring.factories 파일 만들기 

- spring.factories 안에 자동 설정 파일 추가

  - ```properties
    org.springframework.boot.autoconfigure.EnableAutoConfiguration={패키지.configuration_클래스}
    ```

- `mvn install 실행`

- 자동 설정을 추가할 프로젝트의 pom.xml에 <dependency>로 추가



#### Custom AutoConfiguration 2부

- bean이 겹치면 덮어쓰기 현상 발생

  - configuration 프로젝트의 bean에 `@ConditionalOnMissingBean` 을 사용하여 해당 bean이 없을 때만 등록

- `application.properties`에 해당 빈의 property 추가

  - `classProperties` 클래스를 생성하고,  `@ConfigurationProperties("class")` 를 추가

  - pom.xml 파일에 아래 자동 완성 설정 추가

    - ```XML
      <dependency>
      	<groupId>org.springframework.boot</groupId>
      	<artifactId>spring-boot-configuration-processor</artifactId>
      	<optional>true</optional>
      </dependency>
      ```

    - 

  - `Configuration` class 파일에 `@EnableConfigurationProperties(classProperties)` 추가

  - configuration을 등록할 프로젝트에서는 `application.properties`을 통해 bean의 값 설정



#### 내장 서블릿 컨테이너

- 스프링부트는 웹서버가 아니다.

  - 스프링부트의 기본 내장 서버는 **tomcat**이다.
  - 스프링부트 webflux를 사용하게 되면 내장 서버는 **netty** 이다.

- 수동으로 서버 설정

  - 톰캣 객체 생성 
  - 포트 설정 
  - 톰캣에 컨텍스트 추가 
  - 서블릿 만들기 
  - 톰캣에 서블릿 추가 
  - 컨텍스트에 서블릿 맵핑 
  - 톰캣 실행 및 대기

- 스프링 부트에서는 위 과정을 자동으로 설정

  - ServletWebServerFactoryAutoConfiguration (서블릿 웹 서버 생성) 

    - TomcatServletWebServerFactoryCustomizer (서버 커스터마이징) 

    - 서버를 선택할 수 있다.

      ```xml
      <properties>
          <servlet-api.version>3.1.0</servlet-api.version>
      </properties>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
          <exclusions>
              <!-- Exclude the Tomcat dependency -->
              <exclusion>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-tomcat</artifactId>
              </exclusion>
          </exclusions>
      </dependency>
      <!-- Use Jetty instead -->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-jetty</artifactId>
      </dependency>
      ```

  - DispatcherServletAutoConfiguration 

    - 서블릿 만들고 등록
    - 서버에 상관없이 서블릿을 서버에 등록

  - 서버 port는 `application.properties`에 설정

    - `server.port={port}`

    - {port} 값을 0으로 설정하면 랜덤 포트 사용

    - 이벤트 리스너로 포트 확인 가능

      ```java
      @Component
      public class PortListener implements ApplicationListener<ServletWebServerInitializedEvent> {
          @Override
          public void onApplicationEvent(ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
              ServletWebServerApplicationContext applicationContext = servletWebServerInitializedEvent.getApplicationContext();
              System.out.println(applicationContext.getWebServer().getPort());
          }
      }
      ```



#### 독립적으로 실행 가능한 JAR

- `mvn package`를 하면 실행 가능한 JAR 파일 “하나가" 생성 됨. 
  - 결과 jar에 모든 의존성이 포함되어 있다.
  - jar파일의 `BOOT-INF/classes`에 만든 클래스들이 있다.
  - jar파일의 `BOOT-INF/lib`에 필요한 라이브러리들이 있다.
- `spring-maven-plugin`이 해주는 일 (패키징)
- org.springframework.boot.loader.jar.JarFile을 사용해서 내장 JAR를 읽는다. 
- org.springframework.boot.loader.Launcher를 사용해서 실행한다.
- `MANIFEST.MF` 파일의 `Main-Class`가 `JarLuancher`로 설정되어 있다.
  - `Start-Class`가 자신이 만든 애플리케이션 클래스가 설정, 
    - JarLauncher가 정보를 읽어 실행한다. (lib을 읽어서 필요한 jar를 읽어낸다.)



#### SpringApplication

- 1번 주석과 2번 주석은 같은 코드이다.

  - 2번 주석을 사용하면 SpringApplication의 설정을 이용할 수 있다.

  ```java
  public static void main(String[] args) {
  	/** 1 **/
      SpringApplication.run(SpringbootExampleApplication.class, args);
      
      /** 2 **/
      SpringApplication application = new SpringApplication(SpringApplication.class);
      application.run();
  }
  ```

- `resources/banner.txt`를 사용하여 애플리케이션 시작시 배너를 커스터마이징 할 수 있다.

- Event의 시점

  - application 시작 될 때
  - applicationContext를 만들었을 때
  - applicationContext refresh 되었을 때
  - application이 다 구동이 되었을 때
  - application 실행이 실패했을 때 등

- evnet listener 예제 (application이 시작 되었을 때, applicationContext가 생성되지 않음)

  ```java
  @Component
  public class SampleListener implements ApplicationListener<ApplicationStartingEvent> {
      @Override
      public void onApplicationEvent(ApplicationStartingEvent applicationStartingEvent) {
          System.out.println("application is starting");
      }
  }
  ```

  - applicationContext가 만들어지기 전 상황의 listener를 준비할 때

    - 직접 `application class`에bean을 등록해야 한다.

      ```java
      @SpringBootApplication
      public class SpringbootExampleApplication {
          public static void main(String[] args) {
              SpringApplication application = new SpringApplication(SpringApplication.class);
              application.addListeners(new SampleListener());
              application.run();
          }
      }
      ```

- WebApplicationType을 설정할 수 있다.

  - ```java
    app.setWebApplicationType(WebApplicationType.SERVLET);
    app.setWebApplicationType(WebApplicationType.NONE);
    app.setWebApplicationType(WebApplicationType.REACTIVE);
    ```

- JVM option은 application argument가 아니다.

  - `--`옵션 값이 application argument

- 애플리케이션 실행한 뒤 뭔가 실행하고 싶을 때 

  - ApplicationRunner (추천) 또는 CommandLineRunner 
  - 순서 지정 가능 @Order

#### 외부설정

- application.properties

  - 스프링부트 애플리케이션이 구동될 때 자동으로 로딩

    ```java
    some.key="some Value";
                                 aplication.properties
    =========================
    
    @Value("${some.key}")
    private String someValue; (==&qome ValueVat
    ```

- test용 resources는 test/resources/application.properties 파일 생성하여 설정

  - 우선순위로 인해 프로젝트의 application.properties가 먼저 로딩되고 나중에 test폴더의 application.properties가 로딩된다.

  - ```java
    @Autowired
    Environment environment;
    
    @Test
    public void contextLoads(){
    	assertTath(environment.getProperty("some.key"))
    		.isEqualTo("som Value");
    }
    ```

  - **test용 application.properties를 따로 생성하면 기존의 application.properties를 덮어쓴다.**

- application.properties 우선 순위 (높은게 낮은걸 덮어 씀) 

  1. file:./config/ 
  2. file:./ 
  3. classpath:/config/
  4. classpath:/

- bean을 생성하여 properties 값을 바인딩 (**추천**)

  - `@ConfigurationProperties("property이름")` 어노테이션과
  - 멤버 변수의 getter, setter 사용
  - `@Component`를 사용하여 bean으로 등록

- `@Validated` 어노테이션을 설정하여 프로퍼티 값 검정



#### 프로파일

- 특정 상황에 맞게 bean이나 property를 설정 가능
- `@Profile` 어노테이션 사용
- `application.properties` 파일에 `spring.profiles.active={value}` 로 설정 가능
- 프로파일용 프로퍼티 `application-{profile}.properties` 파일을 생성하여 분리 관리 가능
- `spring.profiles.include={value}` application.properties가 로딩될 때 추가할 profile 설정



#### 테스트 

- spring-boot-starter-test를 추가
  - test 스콥으로 추가. 
- `@SpringBootTest`
  - `@RunWith(SpringRunner.class)`랑 같이 써야 함. 
  - 빈 설정 파일은 설정은 자동으로 찾는다.(@SpringBootApplication) 
  - webEnvironment 
    - MOCK: mock servlet environment. 내장 톰캣 구동 안 함. 
    - RANDON_PORT, DEFINED_PORT: 내장 톰캣 사용 함.
    - NONE: 서블릿 환경 제공 안 함. 
- @MockBean 
  - ApplicationContext에 들어있는 빈을 Mock으로 만든 객체로 교체 함.
  - 모든 @Test 마다 자동으로 리셋. 
- 슬라이스 테스트 
  - 레이어 별로 잘라서 테스트하고 싶을 때 
  - `@JsonTest `
  - `@WebMvcTest `
  - `@WebFluxTest `
  - `@DataJpaTest` ...