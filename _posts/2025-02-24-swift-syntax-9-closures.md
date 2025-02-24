---
title: 문법 (9) 클로저 (Closure)
description: Swift의 클로저(Closure) 개념과 활용 방법을 설명합니다.
author: bbdyno
date: 2025-02-24 17:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 클로저, Closure, 함수형 프로그래밍, 고차 함수]
pin: true
math: true
mermaid: true
---

## 클로저 (Closure)

클로저(Closure)는 **코드에서 전달 및 사용할 수 있는 독립적인 코드 블록**입니다.  
Swift의 클로저는 **익명 함수(Anonymous Function)** 와 유사하며, 함수의 일부 기능을 대체할 수도 있습니다.

---

## 1. 클로저의 기본 구조

```swift
{ (매개변수) -> 반환 타입 in
    실행 코드
}
```

### 예제: 간단한 클로저

```swift
let greeting = { (name: String) in
    print("Hello, \(name)!")
}

greeting("Swift")  // Hello, Swift!
```

---

## 2. 클로저를 변수로 저장

클로저는 변수 또는 상수에 저장될 수 있습니다.

```swift
let add: (Int, Int) -> Int = { (a, b) in
    return a + b
}

print(add(3, 5))  // 8
```

---

## 3. 클로저를 함수의 매개변수로 전달

```swift
func operateOnNumbers(_ a: Int, _ b: Int, operation: (Int, Int) -> Int) -> Int {
    return operation(a, b)
}

let result = operateOnNumbers(5, 3, operation: { (x, y) in x + y })
print(result)  // 8
```

---

## 4. 클로저의 간소화 (후행 클로저)

Swift는 클로저 문법을 간결하게 줄일 수 있습니다.

```swift
let result2 = operateOnNumbers(4, 2) { $0 * $1 }
print(result2)  // 8
```

### 클로저 간소화 방식:
1. 매개변수 및 반환 타입 생략 가능
2. `in` 키워드 생략 가능
3. `$0, $1`과 같은 단축 인자 사용 가능

---

## 5. 반환값이 있는 클로저

클로저는 반환값을 가질 수도 있습니다.

```swift
let multiply = { (a: Int, b: Int) -> Int in
    return a * b
}

print(multiply(4, 5))  // 20
```

---

## 6. 클로저의 캡처(Capture)

클로저는 **외부 변수 또는 상수를 캡처하여 유지**할 수 있습니다.

```swift
func makeIncrementer(value: Int) -> () -> Int {
    var total = 0
    return {
        total += value
        return total
    }
}

let incrementByTwo = makeIncrementer(value: 2)
print(incrementByTwo())  // 2
print(incrementByTwo())  // 4
```

- `total` 변수는 `incrementByTwo` 클로저 내부에서 유지됩니다.

---

## 7. 자동 클로저 (Autoclosure)

자동 클로저는 **인수를 감싸서 실행을 지연시키는 역할**을 합니다.

```swift
func printMessage(_ message: @autoclosure () -> String) {
    print("메시지: \(message())")
}

printMessage("Hello, Swift!")  // 메시지: Hello, Swift!
```

---

## 8. 클로저 vs 함수

| 특징 | 클로저 (Closure) | 함수 (Function) |
|------|---------------|----------------|
| 이름 여부 | 없음 (익명 가능) | 있음 |
| 선언 방식 | `{ (매개변수) -> 반환타입 in 실행 코드 }` | `func 함수이름(매개변수) -> 반환타입 { 실행 코드 }` |
| 캡처 가능 여부 | 가능 | 불가능 |

---

## 결론

클로저는 **간결한 코드, 캡처 기능, 고차 함수와의 활용성** 등의 장점이 있습니다.  
- **후행 클로저**와 **자동 클로저**를 활용하면 더욱 효율적인 코드를 작성할 수 있습니다.  
- **고차 함수**와 함께 사용하면 Swift의 함수형 프로그래밍 패턴을 활용할 수 있습니다.  

다음 글에서는 **객체 지향 프로그래밍 (struct, class, enum)** 에 대해 알아보겠습니다.
