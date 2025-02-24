---
title: 문법 (11) 프로퍼티 (저장 프로퍼티, 연산 프로퍼티, lazy)
description: Swift에서 프로퍼티의 개념과 저장 프로퍼티, 연산 프로퍼티, lazy 프로퍼티의 차이점을 설명합니다.
author: bbdyno
date: 2025-02-24 18:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 프로퍼티, 저장 프로퍼티, 연산 프로퍼티, lazy, 속성]
pin: true
math: true
mermaid: true
---

## 프로퍼티 (Property)

Swift에서 프로퍼티(Property)는 클래스(Class), 구조체(Struct), 열거형(Enum) 내부에 **속성을 저장하거나 계산하는 역할**을 합니다.  
프로퍼티는 크게 **저장 프로퍼티(Stored Property)**, **연산 프로퍼티(Computed Property)**, **지연 저장 프로퍼티(Lazy Property)** 로 나뉩니다.

---

## 1. 저장 프로퍼티 (Stored Property)

**값을 저장하는 프로퍼티**로, 구조체(`struct`)와 클래스(`class`)에서 사용할 수 있습니다.

```swift
struct Person {
    var name: String
    var age: Int
}

var person = Person(name: "Alice", age: 25)
print(person.name)  // Alice
```

### 변경 가능한(`var`) 저장 프로퍼티

```swift
struct Car {
    var model: String
    var year: Int
}

var myCar = Car(model: "Tesla", year: 2023)
myCar.year = 2025  // 변경 가능
```

### 변경 불가능한(`let`) 저장 프로퍼티

```swift
struct Dog {
    let breed: String
}

let myDog = Dog(breed: "Golden Retriever")
// myDog.breed = "Husky"  // 오류 발생 (변경 불가)
```

---

## 2. 연산 프로퍼티 (Computed Property)

연산 프로퍼티는 **실제 값을 저장하지 않고, 특정 연산을 통해 값을 반환**합니다.

```swift
struct Circle {
    var radius: Double

    var diameter: Double {
        return radius * 2
    }
}

let circle = Circle(radius: 5)
print(circle.diameter)  // 10.0
```

### `get`과 `set`을 활용한 연산 프로퍼티

```swift
struct Rectangle {
    var width: Double
    var height: Double

    var area: Double {
        get {
            return width * height
        }
        set {
            width = newValue / height
        }
    }
}

var rect = Rectangle(width: 5, height: 10)
print(rect.area)  // 50.0

rect.area = 100
print(rect.width)  // 10.0 (자동 계산됨)
```

- `get`: 값을 반환하는 역할
- `set`: 값을 변경하는 역할 (`newValue` 사용 가능)

---

## 3. `lazy` 프로퍼티 (지연 저장 프로퍼티)

`lazy` 키워드를 사용하면 **값이 처음 사용될 때 초기화**됩니다.

```swift
struct DataLoader {
    init() {
        print("데이터 로딩 중...")
    }
}

struct App {
    lazy var dataLoader = DataLoader()
}

var app = App()
print("앱이 실행됨")
// app.dataLoader가 사용될 때 초기화됨
print(app.dataLoader)
```

- `lazy` 프로퍼티는 **클래스(`class`)에서는 `var`로만 선언 가능** (`let` 불가)
- **메모리를 절약**하며, **값이 필요할 때만 생성**됨

---

## 4. 프로퍼티 옵저버 (`willSet`, `didSet`)

`willSet`과 `didSet`을 사용하면 **프로퍼티 값이 변경될 때 특정 동작을 수행**할 수 있습니다.

```swift
struct Score {
    var value: Int {
        willSet {
            print("점수가 \(value)에서 \(newValue)로 변경됩니다.")
        }
        didSet {
            print("변경 완료: \(oldValue) → \(value)")
        }
    }
}

var myScore = Score(value: 50)
myScore.value = 75
```

출력:
```
점수가 50에서 75로 변경됩니다.
변경 완료: 50 → 75
```

---

## 5. 저장 프로퍼티 vs 연산 프로퍼티 vs `lazy` 프로퍼티 비교

| 특징        | 저장 프로퍼티 (`Stored Property`) | 연산 프로퍼티 (`Computed Property`) | `lazy` 프로퍼티 |
|------------|---------------------------------|---------------------------------|----------------|
| 값 저장 여부 | O (값 저장) | X (계산된 값 반환) | O (값 저장, 필요 시 초기화) |
| `let` 사용 가능 | O | X | X |
| 메모리 사용 | 즉시 할당 | 매번 계산 | 사용 시점에 초기화 |
| 사용 예시 | 고정된 값 저장 | 실시간으로 계산되는 값 | 사용 시점까지 초기화 지연 |

---

## 결론

Swift에서 프로퍼티를 적절히 활용하면 **코드를 최적화**하고 **메모리 사용을 효율적으로 관리**할 수 있습니다.

- **변경될 가능성이 있는 값** → 저장 프로퍼티 사용 (`var`)
- **값을 실시간으로 계산** → 연산 프로퍼티 사용 (`get`, `set`)
- **사용될 때만 초기화** → `lazy` 프로퍼티 활용

다음 글에서는 **메서드 (인스턴스 메서드, 타입 메서드)** 에 대해 알아보겠습니다.
