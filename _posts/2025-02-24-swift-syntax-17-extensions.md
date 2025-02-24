---
title: 문법 (17) 익스텐션 (extension)
description: Swift에서 익스텐션 (extension)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Extension, 기능 확장, 프로토콜 적용, 메서드 추가]
pin: true
math: true
mermaid: true
---

## 익스텐션 (extension)


Swift의 **익스텐션(Extension)** 은 기존의 클래스(Class), 구조체(Struct), 열거형(Enum), 프로토콜(Protocol)에 **새로운 기능을 추가하는 방법**입니다.  
익스텐션을 사용하면 기존 코드에 영향을 주지 않고 **기능을 확장**할 수 있습니다.

## 1. 익스텐션 기본 사용법

```swift
extension Int {
    func squared() -> Int {
        return self * self
    }
}

print(4.squared())  // 16
```

- `Int` 타입에 `squared()` 메서드를 추가하여 **제곱값을 반환하는 기능**을 확장

## 2. 익스텐션을 활용한 기본 타입 확장

```swift
extension String {
    var isEmptyOrWhitespace: Bool {
        return trimmingCharacters(in: .whitespacesAndNewlines).isEmpty
    }
}

print("   ".isEmptyOrWhitespace)  // true
print("Swift".isEmptyOrWhitespace)  // false
```

- `String` 타입에 **공백을 제외한 문자열이 비어있는지 확인하는 프로퍼티** 추가

## 3. 이니셜라이저 추가

익스텐션을 통해 **새로운 생성자(Initializer)** 를 추가할 수 있습니다.

```swift
struct User {
    var name: String
    var age: Int
}

extension User {
    init(name: String) {
        self.name = name
        self.age = 18  // 기본값 설정
    }
}

let newUser = User(name: "Alice")
print(newUser.age)  // 18
```

## 4. 프로토콜 준수 추가

기존 타입에 프로토콜을 적용할 수도 있습니다.

```swift
protocol Describable {
    func describe() -> String
}

extension Int: Describable {
    func describe() -> String {
        return "This is number \(self)"
    }
}

print(42.describe())  // This is number 42
```

- `Int` 타입에 `Describable` 프로토콜을 채택하도록 확장

## 5. 제네릭을 활용한 익스텐션

익스텐션은 **제네릭(Generic)을 활용하여 더욱 유연한 기능을 추가**할 수 있습니다.

```swift
extension Array where Element: Equatable {
    func containsDuplicates() -> Bool {
        return self.count != Set(self).count
    }
}

print([1, 2, 3, 3].containsDuplicates())  // true
print([1, 2, 3].containsDuplicates())  // false
```

- `Array` 타입이 `Equatable`일 때 **중복 요소 여부를 검사하는 메서드** 추가

## 6. 익스텐션을 사용한 네임스페이스 정리

```swift
struct API { }

extension API {
    struct UserService {
        static func fetchUser() {
            print("Fetching user data...")
        }
    }
}

API.UserService.fetchUser()  // Fetching user data...
```

- `API` 네임스페이스 내부에 `UserService` 기능을 모듈화

---

> 익스텐션을 활용하면 **가독성이 좋은 코드**를 작성할 수 있으며, **유지보수성과 재사용성**이 향상됩니다.

