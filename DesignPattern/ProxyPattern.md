---
title: "Proxy"
author: HyunJi Park
date: 2020-03-22
---

# Proxy 패턴이란
![proxyimg](https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Proxy_pattern_diagram.svg/400px-Proxy_pattern_diagram.svg.png)
> + RealSubject 와 Proxy는 같은 인터페이스를 구현합니다.
> + Proxy는 메서드 수행 시 실제 객체의 메서드에 위임합니다.

`Proxy`는 대리자, 대변인 이라는 뜻입니다. 즉, Proxy Pattern이란 다른 객체를 대리하는 프록시를 두는 디자인 패턴입니다.
프록시를 사용하면, 객체에 직접적으로 접근하지 않고 프록시에 접근하기 때문에, 해당 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설정할 수 있습니다. 또한 실제 객체의 기능이 반드시 필요한 시점까지 객체의 생성을 미룰 수 있습니다.(Lazy Initialization)

# Proxy 패턴의 특징
+ 대리자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이때 인터페이스를 사용한다.
+ 대리자는 실제 서비스에 대한 참조 변수를 갖는다.
+ 대리자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 돌려준다.
+ 대리자는 실제 서비스의 메서드 호출 전 후에도 별도의 로직을 수행할 수도 있다.




# 예시

![example](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99CB0A33599FE63613)

## IService.java
```
public interface IService {
	String runSomething();
}
```

## Service.java

```
public class Service implements IService {
	@Override
	public String runSomething() {
		return "서비스 짱!!!";
	}
}
```

## Proxy.java
```
public class Proxy implements IService {
	IService service1;

	@Override
	public String runSomething() {
		System.out.println("호출에 대한 흐름 제어가 주목적, 반환 결과를 그대로 전달");
		service1 = new Service();
		return service1.runSomething();
	}
}
```

## Main.java

```
public class Main {  	
    public static void main(String[] args) { 		
	    //직접 호출하지 않고 프록시를 호출한다. 		
	    IService proxy = new Proxy(); 		
	    System.out.println(proxy.runSomething()); 	
	}
}
```

[예시 출처](https://limkydev.tistory.com/79)


# 프록시의 종류

## 가상 프록시
가상 프록시는 꼭 필요로 하는 시점까지 객체의 생성을 연기하고, 해당 객체가 생성된 것처럼 보이고 싶을 때 사용하는 패턴입니다.

## 보호 프록시
클라이언트의 주체 클래스에 대한 접근을 프록시 클래스에서 제어하는 패턴입니다. 

# 장점
+ 사이즈가 큰 객체(ex: 이미지)가 로딩되기 전에도 프록시를 통해 참조 할 수 있습니다. 
- 실제 객체의 public, protected 메소드들을 숨기고 인터페이스를 통해 노출시킬 수 있습니다.
+ 원래 객체의 접근에 대해서 사전처리를 프록시를 통해 할 수 있습니다.

# 단점
+ 객체를 생성할 때 한 단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하될 수 있습니다.
- 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되야 하는 경우 성능이 저하될 수 있습니다.
+ 로직이 난해해져 가독성이 떨어질 수 있습니다.


# 다른 패턴과 비교

## Proxy Pattern vs Adaptor Pattern  
Adaptor는 wrapped Object에게 다른 인터페이스를 제공하는 역할
Proxy는 같은 인터페이스와 함께 제공, 
Decorator는 보강된 인터페이스와 함께 제공(Decorated된)

## Proxy Pattern vs Decorator Pattern
 둘 다 한 객체가 다른 객체에게 일들을 위임하는 유사한 구조지만, 의도에서 차이가 있다. 
프록시는 그 자신의 서비스 객체의 라이프 사이클을 관리하는데 반해, 데코레이터의 구성은 클라이언트에 의해 관리된다.