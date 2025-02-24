---
title: 문법 (12) 메서드 (인스턴스 메서드, 타입 메서드)
description: Swift에서 메서드 (인스턴스 메서드, 타입 메서드)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Method, Instance Method, Type Method]
pin: true
math: true
mermaid: true
---

## 메서드 (인스턴스 메서드, 타입 메서드)


Swift에서 **메서드(Method)** 는 특정 동작을 수행하는 코드 블록입니다.  
Swift의 메서드는 **인스턴스 메서드(Instance Method)** 와 **타입 메서드(Type Method)** 로 나뉩니다.

## 1. 인스턴스 메서드 (Instance Method)

인스턴스 메서드는 특정 인스턴스에서 호출할 수 있습니다.

```swift
struct Circle {
    var radius: Double

    func area() -> Double {
        return 3.14 * radius * radius
    }
}

let myCircle = Circle(radius: 5)
print(myCircle.area())  // 78.5
```

## 2. 타입 메서드 (Type Method)

타입 자체에서 호출할 수 있는 메서드는 `static` 또는 `class` 키워드를 사용하여 선언합니다.

```swift
struct MathUtils {
    static func square(_ number: Int) -> Int {
        return number * number
    }
}

print(MathUtils.square(4))  // 16
```

> 타입 메서드는 **클래스, 구조체, 열거형**에서 사용할 수 있으며, `class` 키워드는 **클래스에서만** 사용됩니다.

