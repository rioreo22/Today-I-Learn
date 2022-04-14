# item5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

클래스가 자원에 의존하는 경우.

### 코드 5-1 정적 유틸리티를 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다.

```java
public class SpellChecker {
    private static final Lexicon dictionary = ...;

    private SpellChecker() {}
    //...
}
```

### 코드 5-2 싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다.
```java
public class SpellChecker {
    private final Lexicon dictionary = ...;

    private SpellChecker(...) {}
    public static SpellChecker INSTANCE = new SpellChecker(...);
    //...
}
```

### 필드에서 final 한정자를 제거하고 다른 사전으로 교체하는 메서드를 추가 - 어색, 오류 내기 쉬움, 멀티스레드 환경에서는 사용 불가.

## **사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.**
+ 클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야한다.
## 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식
```java
public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictoinary){
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    //...
}
```

+ 자원의 개수, 의존 관계에 상관없이 작동
+ 불변을 보장하여 같은 자원을 사용하려는 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있다.
+ 의존 객체 주입은 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있다.

의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 규모가 큰 프로젝트에서는 대거(Dagger), 주스(Guice), 스프링(Spring) 같은 의존 객체 주입 프레임워크를 사용하면 좋다.


# 핵심 정리
클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안된다. 대신 필요한 자원 혹은 그 자원을 만들어주는 팩터리를 생성자 혹은 정적 팩터리나 빌더에 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.