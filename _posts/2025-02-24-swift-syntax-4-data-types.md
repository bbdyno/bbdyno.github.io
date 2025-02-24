---
title: 문법 (4) 데이터 타입 (Int, Double, String, Bool, Character, Tuple)
description: Swift에서 제공하는 기본 데이터 타입과 사용 방법을 설명합니다.
author: bbdyno
date: 2025-02-24 15:00:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 데이터 타입, Int, Double, String, Bool, Character, Tuple]
pin: true
math: true
mermaid: true
---

## 데이터 타입

Swift는 **강한 타입 언어(Strongly Typed Language)**로, 변수를 선언할 때 특정 데이터 타입을 사용해야 합니다. 이를 통해 코드의 안정성을 높일 수 있습니다.

---

## 1. 정수형 (`Int`)

Swift에서 `Int`는 정수를 저장하는 타입입니다.

```swift
let age: Int = 25
let year = 2025 // 타입 추론에 의해 Int로 자동 지정
```

`Int`는 64비트 플랫폼에서는 `Int64`와 동일하고, 32비트 플랫폼에서는 `Int32`와 동일합니다.

---

## 2. 부동소수점 숫자 (`Double`, `Float`)

Swift에서 실수를 저장하는 타입은 `Double`과 `Float`이 있습니다.

- `Double`: 64비트 부동소수점 숫자 (기본값)
- `Float`: 32비트 부동소수점 숫자

```swift
let pi: Double = 3.14159
let temperature: Float = 36.5
```

---

## 3. 문자와 문자열 (`Character`, `String`)

### 단일 문자 (`Character`)

```swift
let letter: Character = "A"
print(letter)
```

### 문자열 (`String`)

```swift
let message: String = "Hello, Swift!"
print(message)
```

문자열을 다룰 때 다양한 기능을 사용할 수 있습니다.

```swift
let greeting = "Hello"
let name = "Swift"
let fullGreeting = greeting + ", " + name + "!"
print(fullGreeting)
```

---

## 4. 불리언 (`Bool`)

Swift에서 `Bool` 타입은 참(`true`) 또는 거짓(`false`) 값을 저장합니다.

```swift
let isSwiftFun: Bool = true
let isNight = false
```

`if` 문에서 자주 사용됩니다.

```swift
if isSwiftFun {
    print("Swift는 재미있습니다!")
} else {
    print("Swift를 더 배워봐야 합니다.")
}
```

---

## 5. 튜플 (`Tuple`)

튜플은 여러 개의 값을 하나의 그룹으로 묶는 데이터 타입입니다.

```swift
let httpResponse = (200, "OK")
print(httpResponse.0) // 200
print(httpResponse.1) // OK
```

튜플의 요소에 이름을 지정할 수도 있습니다.

```swift
let userInfo = (name: "Alice", age: 30)
print(userInfo.name) // Alice
print(userInfo.age) // 30
```

---

## 6. 데이터 타입 변환

Swift에서는 다른 타입 간 변환이 필요할 때 명시적으로 타입 변환을 해야 합니다.

```swift
let integerNumber = 10
let doubleNumber = Double(integerNumber) // Int → Double 변환
```

문자열을 숫자로 변환할 때는 `Int()` 또는 `Double()`을 사용할 수 있습니다.

```swift
let stringValue = "42"
if let intValue = Int(stringValue) {
    print(intValue * 2) // 84
}
```

---

## 결론

Swift의 데이터 타입을 이해하고 적절히 활용하면 코드의 안정성과 가독성을 높일 수 있습니다. 특히, 타입 변환과 타입 추론을 적절히 활용하는 것이 중요합니다.

다음 글에서는 **Swift의 컬렉션 타입 (Array, Dictionary, Set)**에 대해 다루겠습니다.
