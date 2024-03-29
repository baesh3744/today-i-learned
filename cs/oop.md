# 객체 지향 프로그래밍 (OOP, Object Oriented Programming)

## 객체 지향 프로그래밍이란

객체 지향 프로그래밍 이전의 프로그래밍 패러다임을 살펴보면, 중심이 컴퓨터에 있었습니다. 컴퓨터가 사고하는대로 프로그래밍을 하는 것입니다. 이와 달리, 객체 지향 프로그래밍이란 인간 중심적 프로그래밍 패러다임이라고 할 수 있습니다. 즉, 현실 세계를 프로그래밍으로 옮겨와 프로그래밍하는 것을 말합니다. 현실 세계의 사물들을 "객체"라고 보고, 그 객체로부터 개발하고자 하는 애플리케이션에 필요한 특징들을 뽑아와 프로그래밍하는 것입니다. 이것을 추상화라고 합니다.

### 객체

객체 지향의 가장 기본은 객체이며, 객체의 핵심은 기능을 제공하는 것입니다. 실제로 객체를 정의할 때 사용하는 것은 객체가 제공해야 할 기능이며, 객체가 내부적으로 어떤 데이터를 갖고 있는지로는 정의되지 않습니다. 이러한 기능들을 오퍼레이션 (operation) 이라고 부릅니다. 즉, 객체는 오퍼레이션으로 정의됩니다.

### 시그니처

오퍼레이션의 사용법은 다음 세 가지로 구성됩니다.

-   기능 식별 이름
-   파라미터 및 파라미터 타입
-   기능 실행 결과 값 및 타입

이 세 가지를 시그니처 (signature) 라고 부릅니다.

### 인터페이스

객체가 제공하는 모든 오퍼레이션 집합을 객체의 인터페이스 (interface) 라고 부릅니다.

### 책임

객체가 자신이 제공하는 기능으로 정의된다는 것은 객체마다 자신만이 제공할 수 있는 기능에 대한 책임이 있다고 표현합니다. 객체가 갖는 책임의 크기는 작을수록 좋습니다. 즉, 객체가 제공하는 기능의 개수는 적은 것이 좋은 것입니다. 한 객체에 많은 기능이 포함되면, 그 기능과 관련된 데이터들도 한 객체에 모두 포함됩니다. 이 경우, 객체에 정의된 많은 오퍼레이션들이 데이터들을 공유하는 방식으로 프로그래밍되는데, 이는 절차 지향 방식과 다를 바가 없습니다. 따라서 책임의 크기가 작을수록 유연해지며, 이것을 단일 책임 원칙이라고 합니다.

### 의존성

한 객체가 다른 객체를 이용한다는 것은 실제 구현에서는 한 객체의 코드에서 다른 객체를 생성하거나 다른 객체의 메소드를 호출한다는 것을 의미합니다. 의존의 영향은 꼬리에 꼬리를 문 것처럼 전파됩니다. 이러하다 변경한 여파가 다시 자기 자신까지 변화시킬 수 있는데, 이를 순환 의존이라고 합니다. 이것을 해결하기 위해 사용하는 방법을 의존 역전 원칙이라고 합니다.

### 캡슐화

객체 지향은 기본적으로 캡슐화를 통해서 한 곳의 변화가 다른 곳에 미치는 영향을 최소화합니다. 캡슐화란 객체가 내부적으로 기능을 어떻게 구현하는지를 감추는 것입니다. 내부 기능 구현이 변경되더라도, 그 기능을 사용하는 코드는 영향을 받지 않도록 해줍니다. 즉, 캡슐화는 내부 구현 변경에 유연함을 줍니다.

<br>

## 객체 지향 프로그래밍의 장점

-   이미 작성한 코드에 대한 재사용성이 높습니다. 자주 사용되는 로직을 라이브러리로 만들어두면 계속해서 사용할 수 있으며, 그 신뢰성을 확보할 수 있습니다.
-   라이브러리를 각종 예외사항에 맞게 잘 만들어두면 개발자가 사소한 실수를 하더라도 그 에러를 컴파일 단계에서 잡아낼 수 있으므로 버그 발생이 줄어듭니다.
-   내부적으로 어떻게 동작하는지 몰라도 개발자는 라이브러리가 제공하는 기능들을 사용할 수 있기 때문에 생산성이 높아지게 됩니다.
-   객체 단위로 코드가 나눠져 작성되기 때문에 디버깅이 쉽고 유지보수에 용이합니다.
-   데이터 모델링을 할 때 객체와 매핑하는 것이 수월하기 때문에 요구사항을 보다 명확하게 파악하여 프로그래밍 할 수 있습니다.

<br>

## 객체 지향 프로그래밍의 단점

-   객체 간의 정보 교환이 모두 메시지 교환을 통해 일어나므로 실행 시스템에 많은 오버헤드가 발생하게 됩니다. 하지만 이것은 하드웨어의 발전으로 많은 부분 보완되었습니다.
-   객체가 상태를 갖습니다. 객체 내부에 변수가 존재하고 이 변수를 통해 객체가 예측할 수 없는 상태를 갖게 되어 애플리케이션 내부에서 버그를 발생시킵니다. 이러한 이유로 함수형 프로그래밍 패러다임이 주목받고 있습니다.

<br>

## 객체 지향적 설계 원칙

1. 단일 책임 원칙 (SRP, Single Responsibility Principle)
    - 클래스는 단 하나의 책임을 가져야 하며, 클래스를 변경하는 이유는 단 하나의 이유이어야 합니다.
2. 개방-폐쇄 원칙 (OCP, Open-Closed Principle)
    - 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 합니다.
3. 리스코프 치환 원칙 (LSP, Liskov Substitution Principle)
    - 상위 타입의 객체를 하위 타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 합니다.
4. 인터페이스 분리 원칙 (ISP, Interface Segregation Principle)
    - 인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 합니다.
5. 의존 역전 원칙 (DIP, Dependency Inversion Principle)
    - 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안됩니다.

<br>

## Reference

https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Development_common_sense#object-oriented-programming

https://asfirstalways.tistory.com/177
