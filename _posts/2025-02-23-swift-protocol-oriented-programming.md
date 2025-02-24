---
title: Swift의 프로토콜 지향 프로그래밍
description: Swift의 프로토콜 지향 프로그래밍 개념과 활용법을 swift.org 공식 문서를 기반으로 설명합니다.
author: bbdyno
date: 2025-02-23 18:44:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Protocol-Oriented Programming, POP, OOP, Swift 프로그래밍]
pin: true
math: true
mermaid: true
---
Swift는 프로토콜을 기반으로 하는 프로그래밍 패러다임을 강조합니다. 이는 객체 지향 프로그래밍(OOP)의 한계를 보완하며, 유연하고 확장 가능한 설계를 가능하게 합니다.

### 1. 프로토콜 지향 프로그래밍(POP) vs 객체 지향 프로그래밍(OOP)

객체 지향 프로그래밍(OOP)은 클래스 상속을 기반으로 하며, 코드 재사용성과 구조적 설계를 강조합니다. 그러나 다중 상속의 제한과 대형 클래스 구조의 복잡성이 단점으로 작용할 수 있습니다.

반면, 프로토콜 지향 프로그래밍(POP)은 프로토콜을 사용하여 기능을 정의하고, 이를 여러 타입에서 채택하여 코드의 유연성을 높이는 접근 방식입니다.

### 2. 프로토콜의 기본 개념

```swift
protocol Drivable {
    var speed: Double { get set }
    func accelerate()
}
```

위와 같이 `Drivable` 프로토콜을 정의하고, 해당 프로토콜을 채택하는 여러 타입을 만들 수 있습니다.

### 3. 프로토콜 확장 (Protocol Extensions)

Swift에서는 프로토콜 확장을 통해 기본 구현을 제공할 수 있습니다.

```swift
extension Drivable {
    func accelerate() {
        print("가속 중...")
    }
}
```

이를 통해 모든 `Drivable` 타입이 기본 `accelerate` 동작을 상속받을 수 있습니다.

### 4. 프로토콜과 값 타입의 활용

Swift의 값 타입(구조체, 열거형)과 프로토콜을 함께 사용하면, 참조 타입인 클래스보다 메모리 관리와 성능 면에서 유리합니다.

```swift
struct Car: Drivable {
    var speed: Double = 0.0
}
```

### 5. 프로토콜 합성 (Protocol Composition)

여러 개의 프로토콜을 조합하여 사용할 수도 있습니다.

```swift
protocol Electric {
    func charge()
}

struct Tesla: Drivable, Electric {
    var speed: Double = 0.0
    func charge() {
        print("배터리 충전 중...")
    }
}
```

### 6. 프로토콜 지향 프로그래밍의 장점

- 코드의 유연성과 재사용성을 높일 수 있음
- 다중 상속 문제 없이 여러 기능을 조합할 수 있음
- 값 타입과 결합하여 안전하고 성능이 뛰어난 코드 작성 가능

## 결론

Swift의 프로토콜 지향 프로그래밍(POP)은 객체 지향 프로그래밍(OOP)의 단점을 보완하며, 코드의 유지보수성과 확장성을 높이는 패러다임입니다. 공식 문서와 함께 활용해보세요.

> 참고 문서: [Swift 공식 문서 - Protocols](https://swift.org/documentation/)
