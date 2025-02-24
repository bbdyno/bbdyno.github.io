---
title: 문법 (16) 프로토콜 (protocol)
description: Swift에서 프로토콜 (protocol)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Protocol, Interface, Abstraction]
pin: true
math: true
mermaid: true
---

## 프로토콜 (protocol)


Swift에서 **프로토콜(Protocol)** 은 특정 **속성과 메서드를 정의**하는 청사진(Interface) 역할을 합니다.  
클래스, 구조체, 열거형이 프로토콜을 채택(adopt)하면, 해당 요구사항을 반드시 구현해야 합니다.

## 1. 프로토콜 기본 사용법

```swift
protocol Greetable {
    var greeting: String { get }
    func sayHello()
}

struct Person: Greetable {
    var greeting: String

    func sayHello() {
        print(greeting)
    }
}

let user = Person(greeting: "Hello, Swift!")
user.sayHello() // Hello, Swift!
```

- `Greetable` 프로토콜을 구조체에서 채택하고, 요구된 속성과 메서드를 구현

## 2. 프로퍼티 요구사항

프로토콜은 **읽기 전용(`get`) 또는 읽기/쓰기(`get set`) 프로퍼티**를 요구할 수 있습니다.

```swift
protocol Identifiable {
    var id: String { get set }
}

class User: Identifiable {
    var id: String

    init(id: String) {
        self.id = id
    }
}
```

## 3. 메서드 요구사항

프로토콜은 특정 메서드 구현을 요구할 수 있습니다.

```swift
protocol Runnable {
    func run()
}

class Athlete: Runnable {
    func run() {
        print("Running...")
    }
}

let sprinter = Athlete()
sprinter.run()  // Running...
```

## 4. 프로토콜 상속

하나의 프로토콜은 **여러 개의 프로토콜을 상속**할 수 있습니다.

```swift
protocol Flyable {
    func fly()
}

protocol Swimmable {
    func swim()
}

protocol SuperHero: Flyable, Swimmable {}

struct Superman: SuperHero {
    func fly() {
        print("Flying!")
    }

    func swim() {
        print("Swimming!")
    }
}
```

## 5. 프로토콜 확장 (Protocol Extension)

프로토콜에 **기본 구현을 제공**할 수 있습니다.

```swift
protocol Describable {
    func describe() -> String
}

extension Describable {
    func describe() -> String {
        return "This is a describable object."
    }
}

struct Book: Describable {}
let myBook = Book()
print(myBook.describe())  // This is a describable object.
```

## 6. 프로토콜을 활용한 다형성

프로토콜을 사용하면 **클래스, 구조체, 열거형을 통합적으로 다룰 수 있습니다.**

```swift
func performAction(_ object: Runnable) {
    object.run()
}

performAction(sprinter)  // Running...
```

## 7. `associatedtype`을 활용한 제네릭 프로토콜

```swift
protocol Container {
    associatedtype Item
    func addItem(_ item: Item)
}

struct IntContainer: Container {
    typealias Item = Int

    func addItem(_ item: Int) {
        print("Adding \(item)")
    }
}

let container = IntContainer()
container.addItem(10)  // Adding 10
```

---

> 프로토콜을 사용하면 **객체 간 결합도를 낮추고, 유연한 코드 설계**가 가능합니다.

