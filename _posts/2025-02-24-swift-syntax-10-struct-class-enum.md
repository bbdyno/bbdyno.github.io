---
title: 문법 (10) 구조체 (struct) vs 클래스 (class) vs 열거형 (enum)
description: Swift에서 구조체(struct), 클래스(class), 열거형(enum)의 차이와 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 18:00:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 구조체, 클래스, 열거형, struct, class, enum]
pin: true
math: true
mermaid: true
---

## 구조체 (struct) vs 클래스 (class) vs 열거형 (enum)

Swift에서는 데이터를 구조화하여 다루기 위해 **구조체(struct)**, **클래스(class)**, **열거형(enum)**을 사용할 수 있습니다.  
각 타입의 차이점을 이해하고 적절한 상황에서 사용하는 것이 중요합니다.

---

## 1. 구조체 (`struct`)

구조체는 **값 타입(Value Type)**이며, 인스턴스를 복사할 때 **새로운 복사본**을 생성합니다.

### 구조체 선언

```swift
struct Person {
    var name: String
    var age: Int
}

var person1 = Person(name: "Alice", age: 25)
var person2 = person1 // 복사됨 (새로운 인스턴스)
person2.name = "Bob"

print(person1.name) // Alice
print(person2.name) // Bob
```

- `person1`과 `person2`는 서로 독립적인 메모리 공간을 가짐.

---

## 2. 클래스 (`class`)

클래스는 **참조 타입(Reference Type)**이며, 인스턴스를 복사할 때 **같은 객체를 참조**합니다.

### 클래스 선언

```swift
class Person {
    var name: String
    var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

var personA = Person(name: "Alice", age: 25)
var personB = personA // 같은 객체를 참조
personB.name = "Bob"

print(personA.name) // Bob
print(personB.name) // Bob
```

- `personA`와 `personB`는 같은 객체를 가리키므로 변경 사항이 공유됨.

---

## 3. 열거형 (`enum`)

열거형은 **값 타입**이며, 특정한 값들의 집합을 정의하는 데 사용됩니다.

### 열거형 선언

```swift
enum Direction {
    case north
    case south
    case east
    case west
}

var move = Direction.north
move = .south
```

### 열거형과 연관 값 (Associated Value)

```swift
enum Result {
    case success(data: String)
    case failure(error: String)
}

let response = Result.success(data: "데이터 로드 성공")
```

---

## 4. 구조체 vs 클래스 vs 열거형 비교

| 특징        | 구조체 (`struct`) | 클래스 (`class`) | 열거형 (`enum`) |
|------------|-----------------|-----------------|----------------|
| 타입       | 값 타입          | 참조 타입       | 값 타입        |
| 상속       | 불가능           | 가능            | 불가능         |
| 복사 방식  | 독립적인 복사본 생성 | 같은 객체를 참조 | 독립적인 복사본 생성 |
| 사용 목적  | 단순한 데이터 구조 | 복잡한 객체 및 상속 필요 | 특정한 값의 집합 |

---

## 5. 언제 구조체, 클래스, 열거형을 사용할까?

- **구조체 (`struct`)**: 데이터가 **독립적으로 유지**되어야 할 때 사용 (예: 좌표, 색상, 설정 값)
- **클래스 (`class`)**: **상속이 필요하거나, 객체 간 상태 공유**가 필요한 경우 (예: 네트워크 연결, 데이터 모델)
- **열거형 (`enum`)**: 특정한 값의 **집합을 표현**해야 할 때 (예: 상태, 방향, HTTP 응답 코드)

---

## 결론

Swift에서는 **값 타입(구조체, 열거형)**과 **참조 타입(클래스)**을 구분하여 사용할 수 있습니다.  
적절한 타입을 선택하면 메모리 관리와 코드 안정성을 높일 수 있습니다.

다음 글에서는 **프로퍼티 (저장 프로퍼티, 연산 프로퍼티, lazy)**에 대해 알아보겠습니다.
