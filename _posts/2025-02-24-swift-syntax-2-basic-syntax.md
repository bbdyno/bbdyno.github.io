---
title: 문법 (2) Swift의 기본 문법 구조
description: Swift의 기본 문법과 주요 개념을 소개합니다.
author: bbdyno
date: 2025-02-24 14:00:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 기본 문법, 변수, 상수, 데이터 타입]
pin: true
math: true
mermaid: true
---

## Swift의 기본 문법 구조

Swift는 간결하고 읽기 쉬운 문법을 제공하며, 현대적인 프로그래밍 언어의 특징을 반영하고 있습니다. 이 글에서는 Swift의 기본적인 문법과 핵심 개념을 정리합니다.

---

## 1. 변수와 상수

Swift에서는 `var`를 사용하여 변수를 선언하고, `let`을 사용하여 상수를 선언합니다.

```swift
var name = "Swift"
name = "Swift Programming"

let version = 5.9
// version = 6.0  // 오류 발생: 상수는 변경할 수 없음
```

- `var`는 값을 변경할 수 있음
- `let`은 값을 변경할 수 없는 상수

## 2. 데이터 타입

Swift는 강한 타입을 사용하는 언어이며, 다음과 같은 기본 데이터 타입을 제공합니다.

```swift
let intValue: Int = 42
let doubleValue: Double = 3.14
let stringValue: String = "Hello"
let boolValue: Bool = true
```

타입 추론(Type Inference) 기능을 지원하므로, 명시적으로 타입을 선언하지 않아도 자동으로 추론됩니다.

```swift
let inferredInt = 100   // Int로 추론됨
let inferredDouble = 3.5 // Double로 추론됨
```

## 3. 컬렉션 타입

Swift에서 제공하는 대표적인 컬렉션 타입은 배열(Array), 딕셔너리(Dictionary), 집합(Set)입니다.

### 배열 (Array)

```swift
var fruits = ["Apple", "Banana", "Cherry"]
fruits.append("Mango")
print(fruits)
```

### 딕셔너리 (Dictionary)

```swift
var capitals = ["Korea": "Seoul", "Japan": "Tokyo"]
capitals["USA"] = "Washington D.C."
print(capitals)
```

### 집합 (Set)

```swift
var uniqueNumbers: Set = [1, 2, 3, 3, 2]
print(uniqueNumbers)  // 중복 값이 제거됨
```

## 4. 연산자

Swift는 기본적인 산술 연산자 외에도 다양한 연산자를 제공합니다.

```swift
let sum = 5 + 3        // 덧셈
let difference = 10 - 2 // 뺄셈
let product = 4 * 3    // 곱셈
let quotient = 8 / 2   // 나눗셈
let remainder = 9 % 2  // 나머지
```

비교 연산자도 사용할 수 있습니다.

```swift
let isEqual = (5 == 5)  // true
let isGreater = (10 > 3) // true
```

## 5. 조건문

Swift는 `if`, `switch` 문을 사용하여 조건을 처리합니다.

### if 문

```swift
let age = 20

if age >= 18 {
    print("성인입니다.")
} else {
    print("미성년자입니다.")
}
```

### switch 문

```swift
let weather = "sunny"

switch weather {
case "rainy":
    print("비가 옵니다.")
case "sunny":
    print("날씨가 맑습니다.")
default:
    print("기타 날씨")
}
```

## 6. 반복문

Swift는 `for-in` 반복문과 `while` 반복문을 지원합니다.

### for-in 반복문

```swift
for number in 1...5 {
    print(number)
}
```

### while 반복문

```swift
var count = 3

while count > 0 {
    print("남은 횟수: \(count)")
    count -= 1
}
```

## 7. 함수 (Functions)

Swift에서는 `func` 키워드를 사용하여 함수를 정의합니다.

```swift
func greet(name: String) -> String {
    return "Hello, \(name)!"
}

let message = greet(name: "Swift")
print(message)
```

매개변수 기본값을 설정할 수도 있습니다.

```swift
func greetUser(name: String = "Guest") {
    print("Welcome, \(name)!")
}

greetUser()        // Welcome, Guest!
greetUser(name: "Alice")  // Welcome, Alice!
```

## 결론

Swift의 기본 문법을 익히면 더 복잡한 기능을 구현하는 데 도움이 됩니다. 변수, 상수, 데이터 타입, 컬렉션, 연산자, 조건문, 반복문, 함수 등은 Swift를 배우는 첫 단계에서 꼭 익혀야 할 요소들입니다.

이제 다음 단계로 나아가 Swift의 더 고급 기능을 탐구해 봅시다.
