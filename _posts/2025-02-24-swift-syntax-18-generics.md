---
title: 문법 (18) 제네릭 (Generics)
description: Swift에서 제네릭 (Generics)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Generics, 제네릭, 타입 안전성, 유연한 코드]
pin: true
math: true
mermaid: true
---

## 제네릭 (Generics)


Swift의 **제네릭(Generics)** 은 **유연하고 재사용 가능한 코드**를 작성할 수 있도록 도와주는 기능입니다.  
제네릭을 활용하면 **데이터 타입에 의존하지 않는 코드**를 만들 수 있습니다.

## 1. 제네릭 함수 사용

제네릭 함수는 특정 데이터 타입에 관계없이 **유형을 추론하여 사용할 수 있도록** 설계됩니다.

```swift
func swapValues<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

var x = 10, y = 20
swapValues(&x, &y)
print(x, y)  // 20 10

var str1 = "Hello", str2 = "Swift"
swapValues(&str1, &str2)
print(str1, str2)  // Swift Hello
```

- `T`는 **제네릭 타입**을 나타내며, 호출 시점에 결정됨

## 2. 제네릭 타입 (Generic Type)

제네릭을 활용하여 **유연한 데이터 구조**를 만들 수 있습니다.

```swift
struct Stack<T> {
    private var elements: [T] = []

    mutating func push(_ item: T) {
        elements.append(item)
    }

    mutating func pop() -> T? {
        return elements.popLast()
    }
}

var intStack = Stack<Int>()
intStack.push(5)
intStack.push(10)
print(intStack.pop())  // 10
```

## 3. 제네릭과 프로토콜 (`associatedtype`)

```swift
protocol Container {
    associatedtype Item
    mutating func addItem(_ item: Item)
}

struct Box<T>: Container {
    typealias Item = T
    private var items: [T] = []

    mutating func addItem(_ item: T) {
        items.append(item)
    }
}

var box = Box<String>()
box.addItem("Swift")
```

- `associatedtype`을 사용하면 프로토콜 내에서 **유동적인 타입을 적용**할 수 있음

## 4. 제네릭 제약 (`where` 절 활용)

제네릭에 특정 **제약 조건**을 추가할 수 있습니다.

```swift
func findIndex<T: Equatable>(of value: T, in array: [T]) -> Int? {
    for (index, element) in array.enumerated() {
        if element == value {
            return index
        }
    }
    return nil
}

let numbers = [10, 20, 30, 40]
print(findIndex(of: 30, in: numbers) ?? "Not found")  // 2
```

- `T: Equatable`을 통해 `T` 타입이 **Equatable 프로토콜을 준수해야 함**을 명시

## 5. 제네릭 확장 활용

```swift
extension Stack {
    func peek() -> T? {
        return elements.last
    }
}

var stringStack = Stack<String>()
stringStack.push("Swift")
print(stringStack.peek())  // Swift
```

---

> 제네릭을 활용하면 **유연한 데이터 구조와 타입 안전성을 동시에 유지**할 수 있습니다.

