---
title: 문법 (19) 연산자 정의 및 오버로딩 (operator)
description: Swift에서 연산자 정의 및 오버로딩 (operator)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Operator, Overloading, Custom Operators]
pin: true
math: true
mermaid: true
---

## 연산자 정의 및 오버로딩 (operator)


Swift에서는 연산자(Operator)를 직접 정의하거나, 기존 연산자의 동작을 **커스텀 타입에 맞게 변경**할 수 있습니다.  
이를 **연산자 오버로딩(Operator Overloading)** 이라고 합니다.

## 1. 기본 연산자 오버로딩

```swift
struct Vector {
    var x: Int
    var y: Int
}

func + (lhs: Vector, rhs: Vector) -> Vector {
    return Vector(x: lhs.x + rhs.x, y: lhs.y + rhs.y)
}

let v1 = Vector(x: 2, y: 3)
let v2 = Vector(x: 4, y: 1)
let result = v1 + v2

print(result.x, result.y)  // 6 4
```

- `+` 연산자를 `Vector` 타입에 맞게 오버로딩

## 2. 복합 대입 연산자 (`+=`)

```swift
extension Vector {
    static func += (lhs: inout Vector, rhs: Vector) {
        lhs = lhs + rhs
    }
}

var v3 = Vector(x: 1, y: 2)
v3 += v1
print(v3.x, v3.y)  // 3 5
```

- `+=` 연산자를 `Vector` 타입에 적용

## 3. 비교 연산자 (`==`)

```swift
extension Vector: Equatable {
    static func == (lhs: Vector, rhs: Vector) -> Bool {
        return lhs.x == rhs.x && lhs.y == rhs.y
    }
}

print(v1 == v2)  // false
```

- `Equatable`을 준수하여 `==` 연산자 정의

## 4. 커스텀 연산자 정의

```swift
prefix operator √

prefix func √ (value: Double) -> Double {
    return value.squareRoot()
}

print(√9)  // 3.0
```

- `√` 연산자를 **새롭게 정의하여 제곱근을 반환하는 기능 추가**

## 5. `Comparable` 프로토콜을 활용한 `<`, `>` 오버로딩

```swift
extension Vector: Comparable {
    static func < (lhs: Vector, rhs: Vector) -> Bool {
        return (lhs.x * lhs.x + lhs.y * lhs.y) < (rhs.x * rhs.x + rhs.y * rhs.y)
    }
}

print(v1 < v2)  // false
```

- `Comparable` 프로토콜을 구현하여 `<`, `>` 연산자 사용 가능

---

> 연산자 오버로딩을 활용하면 **코드의 직관성을 높이고, 커스텀 타입을 더욱 자연스럽게 사용할 수 있습니다.**

