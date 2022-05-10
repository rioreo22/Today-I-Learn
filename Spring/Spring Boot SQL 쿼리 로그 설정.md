# Springboot SQL 로그 찍는 법

## SQL 포맷팅
application.yml
```yml
spring:
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        format_sql: true
```


```SQL
insert into member (username, id) values (?, ?)
```
위의 SQL문을 다음과 같이 포맷팅 해줌

```SQL
    insert 
    into
        member
        (username, id) 
    values
        (?, ?)
```

## 1. 첫번째 방법 - 비추
application.yml

``` yml
spring:
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        show_sql: true # System.out으로 SQL문 출력 - 비추
```

## 2. 두번째 방법
application.yml

```yml
logging.level:
  org.hibernate.SQL: debug # 로거를 통한 출력
  org.hibernate.type: trace # 파라미터 까지 출력
```

## 3. 세번째 방법 - 외부 라이브러리 사용 
+ 개발할 때만 쓰거나, 운영시 성능 테스트 해보고 사용할 것
+ 스프링부트 2.6.7에서는 P6spy 1.8.0 버전 사용
+ https://github.com/gavlyukovskiy/spring-boot-data-source-decorator

## P6spy

build.gradle
```groovy
	// https://mvnrepository.com/artifact/com.github.gavlyukovskiy/p6spy-spring-boot-starter
	implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.8.0'
```

```SQL
insert into member (username, id) values (?, ?)
insert into member (username, id) values ('memberA', 1);
```
