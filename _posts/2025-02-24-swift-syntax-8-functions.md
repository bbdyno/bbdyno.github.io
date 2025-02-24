---
title: 문법 (8) 함수 (func)
description: Swift에서 함수를 정의하고 사용하는 방법을 설명합니다.
author: bbdyno
date: 2025-02-24 17:00:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 함수, func, 매개변수, 반환값, 클로저]
pin: true
math: true
mermaid: true
---

## 함수 (Function)

Swift에서 함수(`func`)는 특정 작업을 수행하는 코드 블록입니다. **코드 재사용성을 높이고 가독성을 개선**하는 데 도움을 줍니다.

---

## 1. 기본 함수 선언

함수는 `func` 키워드를 사용하여 선언합니다.

```swift
func greet() {
    print("Hello, Swift!")
}

greet()  // Hello, Swift!
```

---

## 2. 매개변수를 받는 함수

함수는 매개변수를 받아서 동작할 수 있습니다.

```swift
func greetUser(name: String) {
    print("Hello, \(name)!")
}

greetUser(name: "Alice")  // Hello, Alice!
```

---

## 3. 반환값이 있는 함수

```swift
func add(a: Int, b: Int) -> Int {
    return a + b
}

let sum = add(a: 5, b: 3)
print(sum)  // 8
```

---

## 4. 매개변수 기본값

기본값을 설정하면 함수 호출 시 해당 인수를 생략할 수 있습니다.

```swift
func welcomeUser(name: String = "Guest") {
    print("Welcome, \(name)!")
}

welcomeUser()         // Welcome, Guest!
welcomeUser(name: "Bob")  // Welcome, Bob!
```

---

## 5. 가변 매개변수

여러 개의 인수를 받을 때는 가변 매개변수를 사용할 수 있습니다.

```swift
func sumNumbers(numbers: Int...) -> Int {
    return numbers.reduce(0, +)
}

print(sumNumbers(numbers: 1, 2, 3, 4, 5))  // 15
```

---

## 6. 내부 & 외부 매개변수 이름

```swift
func introduce(person name: String, age: Int) {
    print("이름: \(name), 나이: \(age)")
}

introduce(person: "Alice", age: 25)
```

- `person`은 **외부 매개변수 이름** (함수 호출 시 사용)
- `name`은 **내부 매개변수 이름** (함수 내부에서 사용)

---

## 7. 반환값 생략 가능

단일 표현식인 경우 `return` 키워드를 생략할 수 있습니다.

```swift
func multiply(a: Int, b: Int) -> Int {
    a * b
}

print(multiply(a: 4, b: 5))  // 20
```

---

## 8. 중첩 함수 (Nested Function)

함수 내부에 함수를 선언할 수도 있습니다.

```swift
func outerFunction() {
    func innerFunction() {
        print("내부 함수 실행")
    }
    innerFunction()
}

outerFunction()
```

---

## 9. `inout` 매개변수 (값 변경 가능)

`inout`을 사용하면 함수 내부에서 매개변수 값을 변경할 수 있습니다.

```swift
func swapValues(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

var x = 10, y = 20
swapValues(&x, &y)
print(x, y)  // 20, 10
```

---

## 10. 고차 함수와 클로저

Swift에서는 함수를 매개변수로 전달하거나, 함수 자체를 반환할 수 있습니다.

```swift
func operateOnNumbers(_ a: Int, _ b: Int, operation: (Int, Int) -> Int) -> Int {
    return operation(a, b)
}

let result = operateOnNumbers(5, 3, operation: { $0 + $1 })
print(result)  // 8
```

---

## 11. 함수 타입과 반환 함수

함수는 **데이터 타입**으로 사용할 수 있습니다.

```swift
func addTwoNumbers(a: Int, b: Int) -> Int {
    return a + b
}

var operation: (Int, Int) -> Int = addTwoNumbers
print(operation(2, 3))  // 5
```

함수를 반환할 수도 있습니다.

```swift
func chooseOperation(addition: Bool) -> (Int, Int) -> Int {
    return addition ? (+) : (-)
}

let selectedOperation = chooseOperation(addition: true)
print(selectedOperation(10, 5))  // 15
```

---

## 결론

Swift 함수는 **재사용성, 코드 모듈화, 유지보수성**을 높이는 중요한 개념입니다.  
다음 글에서는 **클로저(Closure)**에 대해 다뤄보겠습니다.
