# 정적 타이핑(Static Typing)과 동적 타이핑(Dynamic Typing)

## 정적 타이핑

정적 타이핑은 코드를 작성할 때 컴퓨터적 구조를 명시해줍니다. 즉, 변수를 선언할 때, 변수에 할당할 수 있는 값의 종류 (데이터 타입) 을 사전에 선언합니다. 이를 "명시적 타입 선언"이라고 합니다.

정적 타입 언어는 변수의 타입을 변경할 수 없으며, 변수에 선언한 타입에 맞는 값만 할당할 수 있습니다. 정적 타입 언어는 컴파일 시점에서 타입 체크를 수행합니다. 만약 타입 체크를 통과하지 못한다면, 에러를 발생시키고 프로그램의 실행 자체를 막습니다.

대표적인 정적 타입 언어는 C, C++, Java, Kotlin, Go, Rust 등이 있습니다.

```c
// c 변수에는 1바이트 정수 타입의 값 (-128 ~ 127) 만을 할당할 수 있습니다.
char c;

// num 변수에는 4바이트 정수 타입의 값 (-2,124,483,648 ~ 2,124,483,647) 만을 할당할 수 있습니다.
int num;
```

### 장점

-   코드의 안정성과 정교함이 커집니다.
-   코드를 실행하는 속도가 빠릅니다.
-   코드의 구조를 파악하기 쉽습니다.
-   크고 복잡하며 여러 사람들이 함께 참여하는 프로젝트에 적합합니다.

### 단점

-   코드를 작성하는 시간이 느립니다.
-   코드가 길고 복잡해집니다.
-   처음 프로그래밍을 학습하는 사람에게는 어려울 수 있습니다.

<br>

## 동적 타이핑

동적 타이핑은 코드를 작성하는데 있어 컴퓨터적 구조를 생략합니다. 즉, 변수를 선언할 때 타입을 선언하지 않고, 값을 할당할 때 컴퓨터가 타입을 결정 (타입 추론) 하도록 하는 것입니다. 그리고 재할당에 의해 변수의 타입은 언제든지 동적으로 변할 수 있습니다.

대표적인 동적 타입 언어로는 JavaScript, Python, PHP 등이 있습니다.

```javascript
// JavaScript
var foo;
console.log(typeof foo); // undefined

foo = 3;
console.log(typeof foo); // number

foo = null;
console.log(typeof foo); // object
```

### 장점

-   코드를 작성하는 시간이 빠릅니다.
-   코드의 내용, 로직을 파악하기 쉽습니다.
-   처음 프로그래밍을 학습하는 사람에게 적합합니다.

### 단점

-   코드를 실행하는 속도가 느립니다.

<br>

## Reference

https://github.com/junh0328/prepare_frontend_interview/blob/main/JS.md#%EC%A0%95%EC%A0%81-%ED%83%80%EC%9D%B4%ED%95%91%EC%9D%B4-%EB%AD%94%EA%B0%80%EC%9A%94

https://seongonion.tistory.com/16?category=869672
