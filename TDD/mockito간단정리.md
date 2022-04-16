# 상태기반검증, 행위기반검증
+ 상태기반 검증 : 객체의 상태에 대해 검증, 즉 메소드의 리턴값 , 객체의 필드값? 검증
+ 행위기반 검증 : 객체의 행위가 일어났는지에 대한 검증, 즉, 메소드 호출이 몇 번 일어났는지에 대한 검증

# 테스트 더블

+ Dummy : 딱 인스턴스 정도의 가짜 객체, 메소드들은 기본값, null을 리턴하고 사용하지 않는다.

+ stub : Dummy에서 하드코딩된 리턴값이 있는 정도의 가짜 객체

+ fake : stub에서 실제 객체보다 단순화한 로직이 들어간 가짜 객체

+ spy : 상태들을 기록할 필요가 있을 때

# mock 
행위 기반 테스트를 위해 사용되는 객체

 **상태기반검증으로 충분하다면 굳이 행위기반검증을 하지 않는다.**

 근데 행위 기반 + 상태 기반을 하는 경우가 많아서 mock과 테스트 더블 용어를 혼용하는 경우가 많음.

+ mocking - 테스트 더블 객체를 만든다.

+  mocking 으로 아주 간단히 인스턴스화 한다 - Dummy

 + mocking 객체에 특정 리턴값을 돌려주도록 한다 - stub

 + 호출 확인의 기능을 사용한다 - spy

# Mockito 간단 정리

## 1. Mock 객체 만들기
```java
Foo foo = mock(Foo.class);
```

## 2. 예상값 지정
기존 mock 프레임 워크는 스텁 ->예상 -> 수행-> 검증을 했다면,
mockito는 스텁 -> 수행 -> 검증으로, 바로 스텁 후 결과값으로 검증한다.

## 3. 테스트에 사용할 스텁 만들기.

```java
when(Mock_객체의_메소드).thenReturn(리턴값);
when(Mock_객체의_메소드).thenThrow(예외);
```

단, stub은 필요할 때만 만드는 것이 원칙이다.

## 4. 검증
```java
verify(Mock_객체).Mock_객체의_메소드;
verify(Mock_객체, 호출횟수지정_메소드).Mock_객체의_메소드;
```

### 호출횟수 지정 메소드 종류
+ times(n)
+ never
+ atLeastOnce
+ atLeast(n)
+ atMost(n)

### a. Argument Matcher
|종류|설명|
|:----:|:----|
|any|어떤 객체가 됐든 무방|
|any 타입|anyInteger(), anyBoolean() 등 Java 타입에 해당하는 any 시리즈. `null`이거나 해당 타입이면 만족|
|anyCollection<br>anyCollectionOf| List, Set, Map 등 Collection 객체이면 무방, 둘은 동일하지만 자연스러운 문장을 위해 사용|
|argThat(HamCrestMatcher)|Mockito도 Hamcrest Matcher를 사용한다. Hamcrest에서 사용하던 Matcher를 그대로 이 부분에서 사용 가능|
|eq|Argument Matcher가 한번 사용된 부분에선 Java의 타입을 그대로는 더 이상 쓸 수가 없다.<br>verify(mock).add(anyString(), “item”);<br>즉, 위와 같이는 쓸 수 없다. 이럴 때 아래와 같이 eq를 이용한다.<br>verify(mock).put(anyString(9), eq(“item”));|
|anyVararg|여러 개의 인자를 지칭할 때 사용한다.|
|matches(String regex|정규식 문자열로 인자(argument) 대상을 지칭한다.|
|startswith(String)<br>endWith(String)|특정 문자열로 시작하거나 끝나면 OK|
|anyList<br>anyMap<br>anySet| anyCollection의 좀 더 디테일한 버전이다. 해당 타입이기만 하면 OK|
|isA(Class)|해당 클래스 타입이기만 하면 된다.|
|isNull|Null이면 OK|
|isNotNull|null만 아니면 OK|

### b. 순서 검증
만일 Stub으로 만들어진 Mock 객체 메소드의 호출 순서까지 검증하고 싶다면 `InOrder` 클래스를 이용


## Mockito의 특징적인 기능
1. void 메소드를 Stub으로 만들기
void 메소드는 특별히 리턴할 내용이 없기 때문이 Stub으로 만들 일이 거의 없다. 근데 예외 발생하도록 하려면, stub해야함.

```
doThrow(예외).when(Mock_객체).voidMethod();
```


2. 콜백으로 Stub 만들기: `thenAnswer`

Mock은 보통 하드코딩된 값만 돌려주도록 만들 수 있다. 그런데 특정 Mock 메소드에
대해 실제 로직을 구현하고자 할 때 콜백(CallBack) 기법을 사용한다. 그런데 TDD 자
체에서 권장하는 방식은 아니라는 걸 알아두자

3. 실제 객체를 Stub으로 만들기: Spy
지금까지 인터페이스를 Mock으로 만들어왔다. 그런데 Mockito는 실 객체도 Mock으
로 만들 수 있다. Mockito의 강력한 기능 중 하나인데, 너무 강력해서 오히려 문제가
될 수도 있는 기능이다. 부분 Mocking이라고 불리는 방법으로 서드파티(3rd Party) 제
품, 고칠 수 없는 라이브러리만 남아 있는 코드 등에 대해서만 한정적으로 사용하는 걸
권장한다. Mockito의 저자는 spy 기능을 쓰게 된다면, 그건 이미 뭔가 잘못된 코드를
건드리고 있다는 증거라고 이야기하고 있다

4. 똑똑한 NULL 처리: Smart Nulls
Stub으로 만들지 않은 기본형 외의 값을 리턴하는 메소드는 null 값이 찍힌다. 이렇게
만들어진 Mock 객체들은 종종 NPE(Null Pointer Exception)를 유발한다. 필요에 따
라, 좀 더 유용한 값이 기본값으로 찍히게 만드는 것이 SMART NULLS라는 방식이다.

```java
List mockedList = mock(List.class);
List smartMockedList = mock(List.class, RETURNS_SMART_NULLS);
System.out.println(mockedList.toArray());
System.out.println(smartMockedList.toArray());
-----실행 결과-----
null
[LJava.lang.Object;@3eca90
```

SMART NULLS 규칙
- 기본형 래퍼(primitive wrapper) 클래스는 해당 기본형 값으로 바꾼다.
- String은 “”로 바꾼다.
- 배열은 크기 0인 기본 배열 객체로 만들어준다.
- Collection 계열은 빈 Collection 객체로 만든다.

5.  행위 주도 개발(BDD) 스타일 지원

Mockito는 //given //when //then 식의 행위 주도 개발(Behavior-Driven
Development, BDD) 스타일로 테스트 케이스 작성할 수 있게 지원해준다. BDD 스타
일을 사용하려면 Mockito 클래스 대신 BDDMockito를 static import 한다. 다음은 해
당 예다.



# 마무리
- ## Mock은 Mock일 뿐이다

Mock 객체를 사용해 아무리 잘 작동하는 코드를 만들었다 하더라도, 실제 객체(real
object)가 끼어들어 왔을 때도 잘 동작하리라는 보장은 없다. Mock 객체는 실제 객
체나 실제 시스템에 대해 단지 흉내만, 그것도 오로지 당신이 원하는 형태로만 낼 뿐
이란 걸 잊어서는 안 된다. 결국 언제가 됐든, Mock 객체는 실제 객체로 대체되어 테
스트를 해야 하는 시점이 온다. 따라서 초반부터 실제 객체를 테스트에 사용할 수 있
다면, 그리고 그 비용이 많이 크지 않다면, Mock 객체를 사용하지 말자