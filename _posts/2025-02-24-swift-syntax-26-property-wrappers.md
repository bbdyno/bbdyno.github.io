---
title: 문법 (26) Property Wrappers (프로퍼티 래퍼)
description: Swift에서 Property Wrappers (프로퍼티 래퍼)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Property Wrappers, 프로퍼티 래퍼, 데이터 관리]
pin: true
math: true
mermaid: true
---

## Property Wrappers (프로퍼티 래퍼)


Swift의 **Property Wrappers(프로퍼티 래퍼)** 는 **프로퍼티의 공통적인 동작을 캡슐화**하여 재사용성을 높이는 기능입니다.  
이를 활용하면 **코드를 더 간결하고 효율적으로 관리**할 수 있습니다.

## 1. 기본적인 프로퍼티 래퍼 사용

```swift
@propertyWrapper
struct UpperCase {
    private var value: String = ""

    var wrappedValue: String {
        get { value }
        set { value = newValue.uppercased() }
    }

    init(wrappedValue: String) {
        self.wrappedValue = wrappedValue
    }
}

struct User {
    @UpperCase var name: String
}

var user = User(name: "alice")
print(user.name)  // ALICE
```

- `@propertyWrapper`를 사용하여 **값을 자동으로 대문자로 변환하는 래퍼** 생성

## 2. `projectedValue` 활용

```swift
@propertyWrapper
struct Trimmed {
    private var value: String = ""

    var wrappedValue: String {
        get { value }
        set { value = newValue.trimmingCharacters(in: .whitespacesAndNewlines) }
    }

    var projectedValue: String {
        return "**\(value)**"
    }
}

struct Message {
    @Trimmed var text: String
}

var message = Message(text: "  Swift  ")
print(message.text)  // "Swift"
print(message.$text)  // "**Swift**"
```

- `projectedValue`를 활용하면 **래퍼의 추가 정보 제공 가능**

## 3. 기본값을 가진 프로퍼티 래퍼

```swift
@propertyWrapper
struct DefaultValue {
    var wrappedValue: String = "N/A"
}

struct Product {
    @DefaultValue var name: String
}

let product = Product()
print(product.name)  // "N/A"
```

- **기본값을 제공하는 래퍼**를 활용하여 초기값을 지정 가능

## 4. `Codable`과 함께 사용

```swift
struct User: Codable {
    @Trimmed var username: String
}

let jsonData = 
