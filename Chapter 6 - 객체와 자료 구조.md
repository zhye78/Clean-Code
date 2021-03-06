# Chapter 6 - 객체와 자료 구조

### 자료 추상화

 변수를 private으로 선언하더라도 각 값마다 getter/setter를 제공한다면 구현을 외부로 노출하는 셈이다.

 구현을 제대로 감추려면 추상화가 필요하다. 추상 인터페이스를 제공하여 사용자가 구현을 모르는 채로 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스라고 할 수 있다.

```java
public interface Vehicle {
	//연료 상태를 구체적인 숫자 값으로 알려주며 
	//변수값을 읽어 그대로 반환한다는 것을 알 수 있음
	double getFuelTankCapacityInCallons();
	double getGallonsOfGasoline();
}

public interface Vehicle{
	//연료 상태를 백분율로 알려주어 그 정보가 어디서 오는지 드러나지 않음
	double getPercentFuelRemaining();
}
```

 이처럼 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.

### 자료/객체 비대칭

**객체**: 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개

**자료 구조**: 자료를 그대로 공개하고 별다른 함수는 없음

→ 두 정의는 사실상 정 반대이며, 그 차이가 미치는 영향은 크다.

(p. 120 목록 6-5 ~ p. 121 목록 6-6 참고)

- 6-5의 각 도형 클래스 Square, Rectangle, Circle은 아무런 함수를 제공하지 않고 속성만 존재하는 ‘자료 구조’로 볼 수 있다. Geometry 클래스는 이 도형(자료 구조)들을 다루는 함수를 제공한다.</br>
 이 때, Geometry에 도형을 다루는 새로운 함수를 추가하면, 기존에 있던 도형 클래스들은 아무런 영향을 받지 않는다. 반대로, 새 도형(자료 구조)를 추가한다면, Geometry에 속한 함수를 전부 고쳐야 한다.
- 반면, 6-6의 도형 클래스는 객체 지향적이므로 각 도형 클래스에 필요한 함수가 존재하고, 때문에 Geometry 클래스는 필요가 없어졌다.</br>
 또한, 위와 다르게 새 함수를 추가할 때 도형 클래스 전부를 고쳐야 하고, 새 도형을 추가한다면 기존 함수에 아무런 영향을 미치지 않는다.

→ 다시 말해, 객체 지향에서 어려운 변경은 절차 지향에서는 쉬우며, 절차 지향에서 어려운 변경은 객체 지향에서는 쉽다. 따라서 때로는 단순한 자료 구조와 절차적인 코드가 적합한 상황도 있다.

### 디미터 법칙

 **디미터 법칙**: 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다. (= 객체는 조회 함수로 내부 구조를 공개하면 안 된다.)

 +클래스 C의 메서드 f가 호출할 수 있는 메서드

1. 클래스 C의 메서드
2. f가 생성한 객체의 메서드
3. f 인수로 넘어온 객체의 메서드
4. C 인스턴스 변수에 저장된 객체의 메서드

 But, 위 4가지 상황에서 반환된 객체의 메서드까지는 호출하면 안 됨.

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

- **기차 충돌** </br>
 위같은 코드를 기차 충돌이라 부르는데, 조잡하다고 여겨지므로 아래처럼 나누는 것이 좋다.
    
    ```java
    Options opts = ctxt.getOptions();
    File scratchDir = opts.getScratchDir();
    final String outputDir = scratchDir.getAbsolutePath();
    ```
    
     이 예제가 디미터 법칙을 위반하는지 여부는 각 함수에서 반환되는 것이 객체인지 자료 구조인지에 따라 달라진다. 자료 구조라면 당연히 내부 구조를 노출하므로 디미터 법칙 위반이 아니다.
    
- **잡종 구조** </br>
 때때로 절반은 객체, 절반은 자료 구조인 잡종 구조가 나오는데, 이런 잡종 구조는 새로운 함수는 물론이고 새로운 자료 구조도 축가하기 어려운 구조이므로 되도록 피하는 것이 좋다.
- **구조체 감추기** </br>
 만약 위의 코드에서 ctxt, options, outputDir이 객체라면, 내부 구조를 감추면서 디렉터리의 절대 경로를 가져오도록 줄줄이 사탕같은 코드를 수정해야한다.
    
    ```java
    BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
    ```
    

### 자료 전달 객체

 자료 구조체의 전형적인 형태는 공개 변수만 있고 함수는 없는 클래스인데, **자료 전달 객체(Data Transfer Object)**라고도 부른다.

- **활성 레코드** </br>
 DTO의 특수한 형태로, 대개 save나 find같은 탐색 함수도 제공한다. 이 활성 레코드에 비즈니스 규칙 메서드를 추가하면 자료 구조도 아니고 객체도 아닌 잡종 구조가 나오므로 자료 구조로만 취급하도록 해야 한다.

### 결론

 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하다면 객체가 더 적합하고, 새로운 동작을 추가하는 유연성이 필요하다면 자료 구조와 절차적 코드가 더 적합하다. 알아서 잘 선택하자.
