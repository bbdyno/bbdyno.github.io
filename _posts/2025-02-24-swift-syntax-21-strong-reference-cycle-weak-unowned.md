---
title: 문법 (21) 강한 순환 참조와 해결 방법 (weak, unowned)
description: Swift에서 강한 순환 참조와 해결 방법 (weak, unowned)의 개념과 해결 방법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Retain Cycle, Weak, Unowned, 메모리 관리]
pin: true
math: true
mermaid: true
---

## 강한 순환 참조와 해결 방법 (weak, unowned)


Swift의 **ARC(Automatic Reference Counting)** 는 강한 순환 참조(Retain Cycle)가 발생할 수 있습니다.  
이를 방지하기 위해 **`weak`와 `unowned`** 를 사용합니다.

## 1. 강한 순환 참조 (Retain Cycle)

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

personA = nil  // 해제되지 않음
personB = nil  // 해제되지 않음 (메모리 누수 발생)
```

- `personA`와 `personB`가 서로 강한 참조를 유지하면서 **객체가 해제되지 않는 문제 발생**

## 2. `weak`를 사용한 해결 방법

```swift
class Person {
    var name: String
    weak var friend: Person?  // 약한 참조 사용

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

personA = nil  // John 해제됨
personB = nil  // Emma 해제됨
```

- `weak`를 사용하면 **객체가 해제될 때 자동으로 nil로 설정**되어 강한 순환 참조가 해결됨

## 3. `unowned`를 사용한 해결 방법

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

- `unowned`를 사용하면 참조하는 객체가 해제될 때 자동으로 **nil을 할당하지 않고 무효한 참조로 유지**됨

## 4. `weak` vs `unowned` 비교

| 특징      | `weak` | `unowned` |
|----------|--------|-----------|
| 참조 가능 여부 | nil 가능 | nil 불가능 |
| 사용 예시 | 부모가 자식을 참조할 때 | 자식이 부모를 참조할 때 |
| ARC 관리 방식 | 참조 개수 감소 시 nil 설정 | 참조 개수 감소 시 무효화됨 |

---

> `weak`와 `unowned`를 적절히 사용하면 **메모리 누수를 방지하고, 성능을 최적화**할 수 있습니다.

