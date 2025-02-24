---
title: 문법 (23) 옵셔널 체이닝 (Optional Chaining)
description: Swift에서 옵셔널 체이닝 (Optional Chaining)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Optional Chaining, nil, Safe Coding]
pin: true
math: true
mermaid: true
---

## 옵셔널 체이닝 (Optional Chaining)


옵셔널 체이닝(Optional Chaining)은 **옵셔널이 nil인지 확인하면서 프로퍼티, 메서드, 서브스크립트에 접근**하는 방법입니다.  
이를 활용하면 **nil 값이 있는 경우에도 안전하게 코드 실행을 멈출 수 있습니다.**

## 1. 옵셔널 체이닝의 기본 개념

```swift
class Person {
    var pet: Pet?
}

class Pet {
    var name: String
    init(name: String) {
        self.name = name
    }
}

let user = Person()
print(user.pet?.name)  // nil (안전하게 실행 종료)
```

- `?.` 연산자를 사용하면 **nil이면 실행을 멈추고 nil을 반환**

## 2. 옵셔널 체이닝과 기본값 (`??` 연산자)

```swift
let petName = user.pet?.name ?? "No Pet"
print(petName)  // No Pet
```

- `??` 연산자를 사용하여 기본값 제공 가능

## 3. 옵셔널 체이닝을 통한 메서드 호출

```swift
class User {
    func greet() {
        print("Hello!")
    }
}

var user: User? = User()
user?.greet()  // Hello!

user = nil
user?.greet()  // 실행되지 않음
```

- `?.`을 사용하면 **메서드를 안전하게 호출** 가능

## 4. 옵셔널 체이닝과 배열, 딕셔너리

```swift
var names: [String]? = ["Alice", "Bob", "Charlie"]
print(names?.first)  // Optional("Alice")

var dictionary: [String: Int]? = ["one": 1, "two": 2]
print(dictionary?["two"])  // Optional(2)
```

- `?.`을 사용하면 **컬렉션 요소에도 안전하게 접근 가능**

## 5. 옵셔널 체이닝과 다중 체이닝

```swift
class Company {
    var ceo: Person?
}

var company: Company? = Company()
print(company?.ceo?.pet?.name ?? "No CEO or Pet")  // No CEO or Pet
```

- 여러 개의 옵셔널 체이닝을 활용하면 **안전하게 여러 단계의 접근 가능**

---

> 옵셔널 체이닝을 사용하면 **nil 값이 있을 경우에도 안전하게 코드를 실행할 수 있습니다.**

