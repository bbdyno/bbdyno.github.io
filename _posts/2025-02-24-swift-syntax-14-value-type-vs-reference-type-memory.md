---
title: 문법 (14) 값 타입 vs 참조 타입 (메모리 구조 이해)
description: Swift에서 값 타입 vs 참조 타입 (메모리 구조 이해)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Value Type, Reference Type, Memory Management, Struct, Class]
pin: true
math: true
mermaid: true
---

## 값 타입 vs 참조 타입 (메모리 구조 이해)


Swift에서는 데이터 타입을 **값 타입(Value Type)** 과 **참조 타입(Reference Type)** 으로 구분합니다.  
이 개념을 이해하면 **메모리 관리와 성능 최적화**에 도움이 됩니다.

## 1. 값 타입 (Value Type)

**값 타입은 복사(copy)될 때, 새로운 인스턴스를 생성**합니다.  
Swift의 `struct`, `enum`은 값 타입입니다.

```swift
struct Person {
    var name: String
}

var personA = Person(name: "Alice")
var personB = personA // 복사됨

personB.name = "Bob"

print(personA.name)  // Alice
print(personB.name)  // Bob
```

- `personA`와 `personB`는 서로 다른 메모리 공간을 사용합니다.
- 하나를 변경해도 다른 값에 영향을 주지 않습니다.

## 2. 참조 타입 (Reference Type)

**참조 타입은 복사할 때 동일한 메모리 주소를 공유**합니다.  
Swift의 `class`는 참조 타입입니다.

```swift
class Person {
    var name: String

    init(name: String) {
        self.name = name
    }
}

var personA = Person(name: "Alice")
var personB = personA // 같은 객체를 참조

personB.name = "Bob"

print(personA.name)  // Bob
print(personB.name)  // Bob
```

- `personA`와 `personB`는 동일한 인스턴스를 가리킵니다.
- 하나의 값을 변경하면 다른 값도 변경됩니다.

## 3. 값 타입 vs 참조 타입 비교

| 특징        | 값 타입 (`struct`, `enum`) | 참조 타입 (`class`) |
|------------|----------------------|-----------------|
| 메모리 저장 | 스택(Stack)         | 힙(Heap)        |
| 복사 방식  | 값 자체를 복사        | 참조(주소)를 공유 |
| 변경 영향  | 독립적인 변경 가능    | 공유된 객체 변경 |

## 4. 언제 값 타입을 사용할까?

- 값이 변경될 때 **독립적으로 유지**되어야 하는 경우
- 구조적으로 간단한 데이터 (좌표, 색상, 설정 값)

## 5. 언제 참조 타입을 사용할까?

- **상속이 필요한 경우**
- 여러 개의 인스턴스가 **동일한 객체를 공유**해야 하는 경우 (예: 네트워크 연결)

---

> 값 타입과 참조 타입을 적절히 활용하면 **안정적인 코드와 메모리 최적화**가 가능합니다.

