# Chapter 2 - 의미 있는 이름

이번 장에서는 변수나 메서드 등의 이름을 잘 짓는 간단한 규칙 몇 가지를 소개한다.

### 의도를 분명히 밝혀라

 코드가 하는 일이 짐작이 가능하도록 의도가 분명하게 변수/메서드 이름을 지어야 한다.

### 그릇된 정보를 피하라

1.  나름 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하지 않는다.
(ex. list: 실제 List 자료구조가 아니라면, 어떠한 목록을 표현해야 하더라도 ‘list’를 변수명으로 사용하지 않는다.)
2.  서로 흡사한 이름을 사용하지 않는다.
(ex. GroupInfoResponse & GroupResponse : 둘 다 그룹에 관한 정보를 반환해주기 위한 response 객체이지만, 가지고 있는 필드와 사용되는 곳이 명확히 달랐기때문에 그 정보를 함께 표기하는 것이 나을 듯 하다. 
→ GroupInfoResponse & CreateGroupResponse or GroupDetailResponse & GroupSimpleResponse)

### 의미있게 구분하라

(ex. 컴파일 에러만 피하려고 이름을 name, name2 이런식으로 짓지 말자.)

### 발음하기 쉬운 이름을 사용하라

 발음하기 어려운 이름은 토론하기도 어렵다.

(ex. DtaRcrd v

### 검색하기 쉬운 이름을 사용하라

 (ex. 코드상의 숫자 100은 MAX_NUMBER 같은 static final 변수로 만들어서 검색이 쉽도록 한다.)

### 인코딩을 피하라

 꼭 필요한 경우(인터페이스 클래스(XXXService)와 구현 클래스(XXXServiceImpl))를 제외하고는 이름에 인코딩 정보를 포함하지 않도록 한다.

### 자신의 기억력을 자랑하지 마라

 코드를 읽는 사람이 자신이 아는 이름으로 변환할 필요가 없도록 누구나 알 수 있는 이름을 사용해라.

### 클래스 이름 &  메서드 이름

 클래스 이름은 명사/명사구, 메서드 이름은 동사/동사구로 통일한다.

### 기발한 이름은 피한다

 자신만 아는 기발한 이름은 남들이 볼 때 한 번에 이해하기 힘들 수 있으니 피한다.

### 한 개념에 한 단어를 사용하라

 주석을 뒤져보지 않아도 개발자가 올바른 메서드를 선택할 수 있도록 일관성있는 어휘를 사용한다.

(ex. 같은 메서드를 클래스마다 get, fetch, retrieve 등 여러 어휘로 표현하지 않는다.)

### 말장난을 하지 마라

 한 단어를 두 가지 목적으로 사용하지 말아야 한다.

(ex. 두 값을 더하는 add 메서드와 일관성을 유지하기 위해, 집합에 값 하나를 추가하는 메서드도 add라고 명명하지 않는다.)

### 해법 영역/문제 영역에서 가져온 이름을 사용하라

 코드를 읽을 사람도 개발자일 것이기 때문에, 전산 용어나 알고리즘 용어같은 기술적인 이름을 사용하는 것은 현명한 방법이라고 할 수 있다. (ex. JobQueue, AccountVisitor 등)

 단, 적절한 프로그래머 용어가 없을 시 문제 영역에서 이름을 가져오도록 한다.

### 의미있는 맥락을 추가하라

 (ex. street, city, state, name은 전부 한 주소의 일부를 나타내는 변수들인데 하나하나 보면 그 사실을 알아채기 힘들다.
 → addrStreet, addrCity, addrState, addrName으로 맥락을 추가해주는것이 좋다.
 → Address 라는 클래스를 생성하고 클래스의 변수로 추가하는 것이 더 좋은 방법이 된다.

### 불필요한 맥락을 없애라

 반대로, 불필요한 맥락이라면 없애는 것이 좋다. 

(ex. Gas Station Deluxe 라는 애플리케이션을 만든다 해서 모든 클래스명을 GSD로 시작하도록 만들 필요가 없다.)