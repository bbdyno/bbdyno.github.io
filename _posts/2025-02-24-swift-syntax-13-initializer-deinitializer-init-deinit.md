---
title: 문법 (13) 생성자와 소멸자 (init, deinit)
description: Swift에서 생성자와 소멸자 (init, deinit)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Initializer, Deinitializer, Init, Deinit]
pin: true
math: true
mermaid: true
---

## 생성자와 소멸자 (init, deinit)


Swift에서 **생성자(Initializer, `init`)** 는 인스턴스를 생성하는 특별한 메서드이며,  
**소멸자(Deinitializer, `deinit`)** 는 인스턴스가 메모리에서 해제될 때 실행됩니다.

## 1. 기본 생성자 (Initializer)

```swift
class Person {
    var name: String

    init(name: String) {
        self.name = name
    }
}

let user = Person(name: "Alice")
print(user.name)  // Alice
```

## 2. 편의 생성자 (`convenience`)

```swift
class Rectangle {
    var width: Double
    var height: Double

    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }

    convenience init(squareSide: Double) {
        self.init(width: squareSide, height: squareSide)
    }
}
```

## 3. 소멸자 (`deinit`)

클래스에서만 사용되며, 객체가 해제될 때 호출됩니다.

```swift
class FileHandler {
    init() {
        print("파일을 엽니다.")
    }
    
    deinit {
        print("파일을 닫습니다.")
    }
}

var handler: FileHandler? = FileHandler()
handler = nil  // "파일을 닫습니다." 출력
```

