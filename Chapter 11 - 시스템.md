# Chapter 11 - 시스템


## 도시를 세운다면?

 &nbsp;도시에는 큰 그림을 그리는 사람들도 있으며 작은 사항에 집중하는 사람들도 있으며, 도시가 돌아가는 이유는 적절한 추상화와 모듈화 때문이다.</br>

 &nbsp;소프트웨어 팀도 도시처럼 구성한다. 하지만 깨끗한 코드를 구현하면 낮은 수준의 추상화가 쉬워지는데, 이 장에서는 높은 추상화 수준, 즉 **시스템 수준**에서도 깨끗함을 유지하는 방법을 살펴본다.


## 시스템 제작과 시스템 사용을 분리하라

&nbsp;**제작**과 **사용**은 아주 다르다는 것을 명심하고 이 부분을 살펴보자.</br></br>
&nbsp;소프트웨어 시스템은 준비 과정과 런타임 로직을 분리해야 한다.</br>
&nbsp;시작 단계는 모든 애플리케이션이 풀어야 할 **관심사**다. **관심사 분리**는 우리 분야에서 가장 오래되고 중요한 설계 기법이지만, 대다수 애플리케이션은 시작 단계라는 관심사를 분리하지 않고, 준비 과정 코드를 런타임 로직과 마구 뒤섞는다.

```java
public Service getService() {
	if(service == null)
        service = new MyServiceImpl(...);
    return service;
}
```

&nbsp;위 코드는 **초기화 지연/계산 지연** 이라는 기법이다.</br>

- 장점</br>
&nbsp;필요할 때에만 객체를 생성하므로 불필요한 부하가 걸리지 않고, 어떤 경우에도 null을 반환하지 않는다</br>

- 단점</br>
&nbsp;getService() 메서드가 MyServiceImpl 객체에 의존하게 되고, 런타임 로직에서 MyServiceImpl 객체를 전혀 사용하지 않더라도 의존성 해결이 안되면 컴파일이 불가능하다.</br>
&nbsp;또한, MyServiceImpl때문에 테스트 전용 객체를 사용해야하며, 일반 런타임 로직에 객체 생성 로직을 섞어놓은 탓에 모든 실행 경로도 테스트해야한다는(책임이 하나가 아니라는) SRP를 위반하는 단점이 있다. 

### Main 분리

&nbsp;시스템 생성과 사용을 분리하는 한 가지 방법으로, 생성과 관련한 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다.

### 팩토리

&nbsp;객체가 생성되는 시점을 애플리케이션이 결정할 필요가 있을 때 사용된다. 

### 의존성 주입

&nbsp;**의존성 주입**은 사용과 제작을 분리하는 강력한 메커니즘이고, 제어 역전 기법을 의존성 관리에 적용한다. 의존성 자체를 인스턴스로 만드는 책임은 지지 않고, 다른 전담 메커니즘에 넘겨 제어를 역전한다.

```java
MySerivce service = (MyService) (jndiContext.lookup("NameOfMyService"));
```

&nbsp;위 코드를 호출하는 객체는 실제로 lookup 메서드가 무엇을 반환하는지 그 유형을 제어하지 않으면서 의존성을 해결할 수 있다.</br>

&nbsp;진정한 의존성은 여기서 한 발 더 나아간다. 클래스가 직접 의존성을 해결하지 않고, 생성자/setter 등을 통해 DI 컨테이너가 해당 의존성을 설정하도록 한다. (대표적인 예시: Spring Framework)


## 확장

&nbsp;처음부터 시스템을 제대로 제대로 만든다는 것은 미신이고, 오늘 필요한만큼 만든 것을 내일 확장하면 된다. 이것이 애자일 방식의 핵심이며, 테스트 주도 개발, 리팩터링, 깨끗한 코드는 코드 수준에서 이를 도와준다.

&nbsp;시스템 수준도 마찬가지이다. 우리가 관심사들을 적절히 분리만 한다면, 소프트웨어 시스템은 물리적인 시스템과는 다르게 점진적으로 커질 수 있다.

### 횡단(cross-cutting) 관심사

&nbsp;원론적으로는 모듈화, 캡슐화가 가능하지만 현실적으로는 코드가 온갖 객체로 흩어지는 관심사를 **횡단 관심사**라고 한다.</br>

&nbsp;관점 지향 프로그래밍(AOP)은 횡단 관심사에 대처해 모듈성을 확보하는 방법이다. 이처럼 자바에서 사용하는 관점 혹은 관점과 유사한 메커니즘 세 개를 살펴보자.


## 자바 프록시

&nbsp;자바 프록시는 단순한 상황에 적합하다. 

```java
// Bank.java (패키지 이름을 감춘다)
import java.utils.*;

// 은행 추상화
public interface Bank {
    Collection<Account> getAccounts();
    void setAccounts(Collection<Account> accounts);
}

// BankImpl.java
import java.utils.*;

// 추상화를 위한 POJO(Plain Old Java Object)
public class BankImpl implements Bank {
    private List<Account> accounts;

    public Collection<Account> getAccounts() {
        return accounts;
    }
    
    public void setAccounts(Collection<Account> accounts) {
        this.accounts = new ArrayList<Account>();
        for (Account account: accounts) {
            this.accounts.add(account);
        }
    }
}

// BankProxyHandler.java
import java.lang.reflect.*;
import java.util.*;

// 프록시 API가 필요한 “InvocationHandler”
public class BankProxyHandler implements InvocationHandler {
    private Bank bank;
    
    public BankHandler (Bank bank) {
        this.bank = bank;
    }
    
    // InvocationHandler에 정의된 메서드
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String methodName = method.getName();
        if (methodName.equals("getAccounts")) {
            bank.setAccounts(getAccountsFromDatabase());
            
            return bank.getAccounts();
        } else if (methodName.equals("setAccounts")) {
            bank.setAccounts((Collection<Account>) args[0]);
            setAccountsToDatabase(bank.getAccounts());
            
            return null;
        } else {
            ...
        }
    }
    
    // 세부사항은 여기에 이어진다..
    protected Collection<Account> getAccountsFromDatabase() { ... }
    protected void setAccountsToDatabase(Collection<Account> accounts) { ... }
}

// 다른 곳에 위치하는 코드

Bank bank = (Bank) Proxy.newProxyInstance(
    Bank.class.getClassLoader(),
    new Class[] { Bank.class },
    new BankProxyHandler(new BankImpl())
);
```
&nbsp;위 코드에서는 프록시로 감쌀 인터페이스 Bank, 비즈니스 논리를 구현하는 POJO인 BankImpl, InvocationHandler를 넘겨받은 BankProxyHandler 등이 구현되어있는 것을 확인할 수 있다.</br>

&nbsp;여기서 주목해야할 것은, *코드가 너무 길고 복잡하다*는 것이다. 프록시를 사용하면 깨끗한 코드를 작성하기 어렵다.


## 순수 자바 AOP 프레임워크
&nbsp;다행히 대부분의 프록시 도구는 도구로 자동화할 수 있다. 순수 자바 관점의 스프링 AOP, JBoss AOP 등이 대표적이다. 


## AspectJ 관점

