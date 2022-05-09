# Spring Boot Database Test with JPA, 테스트용 Embedded H2 간단 세팅

## 1. 라이브러리 추가
pom.xml
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
```


## 2. test Profile 추가
application-test.yml

```yml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  jpa:
    properties:
      hibernate:
        dialect: org.hibernate.dialect.H2Dialect
```

## 3. 테스트 클래스에 @DataJpaTest, @ActiveProfiles("test") 추가

```java
@DataJpaTest
@ActiveProfiles("test")
class CustomerRepositoryTest {

    @Autowired
    private CustomerRepository customerRepository;

    @Test
    void test() {
        //...
    }
}
```

+ @DataJpaTest에는 @Transactional, @ExtendWith(SpringExtension.class)가 포함되어있다.

```java
@Target(value=TYPE)
 @Retention(value=RUNTIME)
 @Documented
 @Inherited
 @BootstrapWith(value=org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTestContextBootstrapper.class)
 @ExtendWith(value=org.springframework.test.context.junit.jupiter.SpringExtension.class)
 @OverrideAutoConfiguration(enabled=false)
 @TypeExcludeFilters(value=DataJpaTypeExcludeFilter.class)
 @Transactional
 @AutoConfigureCache
 @AutoConfigureDataJpa
 @AutoConfigureTestDatabase
 @AutoConfigureTestEntityManager
 @ImportAutoConfiguration
public @interface DataJpaTest
```


## 참고
[Spring Boot Test Tutorial - Database Testing with Test Containers](https://programmingtechie.com/2020/10/21/spring-boot-testing-tutorial-database-testing-with-test-containers/)