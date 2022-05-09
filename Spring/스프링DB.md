# JDBC (Java Database Connectivity)
대표적으로 다음 3가지 기능을 표준 인터페이스로 정의해서 제공
+ `jdbc.sql.Connection`
+ `jdbc.sql.Statement`
+ `jdbc.sql.ResultSet`


# SQL Mapper
+ 장점 : JDBC를 편리하게 사용하도록 도와준다
    + SQL 응답 결과를 객체로 편리하게 변환해준다.
    + JDBC의 반복 코드를 제거해준다.
+ 단점 : 개발자가 SQL을 직접 작성해야한다.
+ 대표 기술 : 스프링 JdbcTemplate, MyBatis
# ORM
+ 객체를 관계형 데이터베이스 테이블과 매핑해주는 기술
+ 개발자 대신 SQL을 동적으로 만들어 실행해준다. 
+ 대표 기술 : JPA, 하이버네이트, 이클립스링크

둘 다 내부적으로는 JDBC를 사용한다.


# 요약
+ DriverManager, Datasource
커넥션을 얻어오는 방법 추상화, 둘의 사용 차이 있음

+ JdbcUtils 
    + closeResultSet....  자원 반환
    + 보니까 `getResultSetValue` 이런 것도 있고 이것저것 기능 많네..?

+ Connection을 Param으로 넘겨주고 Autocommit false 처리를 해주면서 트랜잭션을 구현할 수 있다.

+ `DataSourceUtils`
    + `getConnection()` - 커넥션 풀에서 커넥션 가져오거나 없으면 생성해서 가져옴
    + `releaseConnection()` - 커넥션을 close하는게 아니라 다시 커넥션보관소에 반환

+ `TransactionManager`
    + Transaction의 status를 관리
    + commit, rollback

+ `TransactionTemplate`
    + 비즈니스 로직이 정상 수행되면 커밋
    + 언체크 예외가 발생하면 롤백, 체크 예외가 발생하면 커밋


+ 람다에서는 체크예외를 밖으로 던질 수가 없군..

# 트랜잭션
+ 템플릿 콜백 패턴 알아보기
+ 애플리케이션 로직 - 핵심 기능(비즈니스 로직) + 부가 로직 (트랜잭션..)
+ 서비스 로직은 가급적 핵심 비즈니스 로직만 있어야함
+ 트랜잭션 AOP에는 PlatformTransactionManager(dataSource())가 필요하다
+ @Transactional 동작하려면 AOP 동작하려면 스프링 빈으로 등록해줘야함
+ AOPUtils.isProxy


-> Spring이 제공하는 Utils 좀 찾아봐야겠다

# 예외처리

+ catch 문에서 Throwable 문을 잡으면 Error도 함께 잡기 때문에, 잡으면 안됨
+ 체크 예외 -> 컴파일러가 체크함
+ 언체크 예외 -> 컴파일러가 체크하지 않음