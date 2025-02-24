---
title: 문법 (20) ARC와 메모리 관리 (Automatic Reference Counting)
description: Swift에서 ARC와 메모리 관리 (Automatic Reference Counting)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, ARC, 메모리 관리, Automatic Reference Counting, Retain Cycle]
pin: true
math: true
mermaid: true
---

## ARC와 메모리 관리 (Automatic Reference Counting)


Swift는 **ARC(Automatic Reference Counting)** 를 사용하여 메모리를 자동으로 관리합니다.  
ARC는 객체의 **참조 횟수를 추적하여, 더 이상 필요하지 않으면 자동으로 해제**합니다.

## 1. ARC의 기본 개념

```swift
class Person {
    var name: String

    init(name: String) {
        self.name = name
        print("\(name) 객체가 생성됨.")
    }

    deinit {
        print("\(name) 객체가 메모리에서 해제됨.")
    }
}

var person1: Person? = Person(name: "Alice")
person1 = nil  // Alice 객체가 메모리에서 해제됨.
```

- `init`에서 객체 생성 로그 출력  
- `deinit`에서 객체 해제 로그 출력

## 2. 강한 참조 순환 문제 (Retain Cycle)

ARC는 **모든 객체가 참조를 유지하고 있으면 해제되지 않는 문제**가 발생할 수 있습니다.

```swift
class Person {
    var name: String
    var friend: Person?

    init(name: String) {
        self.name = name
    }

    deinit {
        print("\(name) 해제됨")
    }
}

var personA: Person? = Person(name: "John")
var personB: Person? = Person(name: "Emma")

personA?.friend = personB
personB?.friend = personA

personA = nil  // 해제되지 않음 (강한 순환 참조 발생)
personB = nil  // 해제되지 않음
```

- `personA`와 `personB`가 서로를 **강한 참조**하고 있어 ARC가 해제하지 못함

## 3. `weak`를 사용한 해결 방법

```swift
class Person {
    var name: String
    weak var friend: Person?  // 약한 참조(Weak Reference)

    init(name: String) {
        self.name = name
    }

    deinit {
        print("\(name) 해제됨")
    }
}

var personA: Person? = Person(name: "John")
var personB: Person? = Person(name: "Emma")

personA?.friend = personB
personB?.friend = personA

personA = nil  // 메모리 해제됨
personB = nil  // 메모리 해제됨
```

- `weak`를 사용하여 한 쪽의 참조를 약한 참조로 변경하면 **강한 순환 참조를 방지**할 수 있음

## 4. `unowned`를 사용한 해결 방법

`unowned`는 **객체가 항상 존재한다고 가정할 때 사용**됩니다.

```swift
class Owner {
    var name: String
    var card: CreditCard?

    init(name: String) {
        self.name = name
    }

    deinit {
        print("\(name) 해제됨")
    }
}

class CreditCard {
    var number: String
    unowned let owner: Owner  // 카드가 항상 주인을 가짐

    init(number: String, owner: Owner) {
        self.number = number
        self.owner = owner
    }

    deinit {
        print("카드 \(number) 해제됨")
    }
}

var owner: Owner? = Owner(name: "Alice")
owner?.card = CreditCard(number: "1234-5678-9012", owner: owner!)
owner = nil  // Alice 해제됨, 카드도 함께 해제됨
```

- `unowned`는 객체가 해제될 때 **자동으로 참조를 무효화**함

## 5. `weak` vs `unowned` 비교

| 특징      | `weak` | `unowned` |
|----------|--------|-----------|
| 참조 가능 여부 | nil 가능 | nil 불가능 |
| 사용 예시 | 부모가 자식을 참조할 때 | 자식이 부모를 참조할 때 |

---

> ARC를 이해하고 **weak, unowned**를 적절히 사용하면 **메모리 누수를 방지하고 최적화**할 수 있습니다.

