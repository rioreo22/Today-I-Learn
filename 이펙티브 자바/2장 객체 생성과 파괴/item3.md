# item3. private 생성자나 열거 타입으로 싱글턴임을 보증하라
+ 싱글턴(singleton)이란 인스턴스를 오직 하나만 생성할 수 있는 클래스
    + 함수와 같은 무상태(stateless) 객체
    + 설계상 유일해야 하는 시스템 컴포넌트

+ **클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.**
    + 싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없기 때문


## 싱글턴을 만드는 방식
+ 생성자는 private로 감추고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버 하나 마련

## 1. public static 멤버가 final 필드인 방식
```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public void leaveTheBuilding() { ... }
}
```

일반적으로 클라이언트는 private 생성자에 접근 못 함.

예외 - 권한이 있는 클라이언트는 리플렉션 API인 `AcessibleObject.setAccessible`을 사용해 private 생성자를 호출할 수 있다.

방어 - 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { 
        if(INSTANCE != null) throw Exception(); // 이런식으로?
     }

    public void leaveTheBuilding() { ... }
}
```

> 리플렉션 API : 구체적인 클래스 타입을 알지 못해도 그 클래스의 정보 (메서드, 타입, 변수 등등)에 접근할 수 있게 해주는 자바 API

### 장점
+ 이 클래스가 싱글턴임이 API에 명백히 드러난다.
+ 간결하다.


## 2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식
```java
public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() {return INSTANCE;}

    public void leaveTheBuilding() { ... }
}
``` 

Elvis.getInstance는 항상 같은 객체의 참조를 반환한다.

리플렉션 API에 대한 예외는 동일하다.

### 장점
1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
1. 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
1. 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다. (예: `Elvis::getInstance`)



## 3. 원소가 하나인 열거 타입을 선언하는 방식
```java 
public enum Elvis{
    INSTANCE;

    public void leaveTheBuilding() { ... }
}
``` 
### 장점
+ public 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화 가능
+ 리플렉션 공력에도 제 2의 인스턴스가 생기는 일을 막아준다.
### 단점
+ 단, Enum 외의 클래스를 상속해야하는 싱글턴이라면 사용할 수 없다.

## **"대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다."**



# 싱글턴 클래스의 직렬화

private로 생성자를 감추고 public static 멤버를 하나 마련하는 방식 1, 2로 만든 싱글턴 클래스를 직렬화하려면, 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다. 

+ 모든 인스턴스 필드를 일시적(transient)라고 선언하고 readResolve 메서드를 제공해야한다. (아이템 89)

```java
//싱글턴임을 보장해주는 readResolve 메서드
public Object readResolve(){
    //'진짜' Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.
    return INSTANCE;
}
```

+ 그렇지 않으면, 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.

## 역직렬화
+ 역직렬화는 기본 생성자를 호출하지 않고 값을 복사해서 인스턴스를 반환한다.
+ 그 때 통하는 것이 readResolve 메서드
+ 역직렬화 시 반환되는 인스턴스를 관장하는 애가 readResolve 메서드.
+ transient 키워드를 써서 제외하는게 중요한게아니라 readResolve 에서 INSTANCE 를 반환하는게 새로운 인스턴스 생성을 방지하는것

# 더 알아볼 것

+ enum의 동작 방식
+ 직렬화, 역직렬화의 정확한 동작.