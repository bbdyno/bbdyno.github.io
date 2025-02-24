---
title: 문법 (22) 옵셔널 (Optional)
description: Swift에서 옵셔널 (Optional)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Optionals, nil, Optional Binding, Optional Chaining]
pin: true
math: true
mermaid: true
---

## 옵셔널 (Optional)


Swift에서는 **옵셔널(Optional)** 을 사용하여 **값이 있을 수도 있고, 없을 수도 있는 상황**을 표현할 수 있습니다.  
옵셔널은 **안전한 코드 작성**을 도와주는 중요한 개념입니다.

## 1. 옵셔널 선언과 기본 사용

```swift
var name: String? = "Alice"
print(name)  // Optional("Alice")
```

- `String?` 타입은 `String` 또는 `nil`을 저장할 수 있음

## 2. 옵셔널 값 추출 (`!` 강제 언래핑)

```swift
var name: String? = "Alice"
print(name!)  // Alice
```

- `!`를 사용하여 **강제 언래핑(Forced Unwrapping)**  
- **주의:** 값이 `nil`이면 **런타임 오류 발생**

## 3. 안전한 옵셔널 바인딩 (`if let`)

```swift
var name: String? = "Alice"

if let unwrappedName = name {
    print("이름: \(unwrappedName)")
} else {
    print("이름이 없습니다.")
}
```

- 옵셔널 값을 안전하게 추출할 수 있음

## 4. `guard let`을 사용한 옵셔널 해제

```swift
func greet(name: String?) {
    guard let unwrappedName = name else {
        print("이름이 없습니다.")
        return
    }
    print("안녕하세요, \(unwrappedName)님!")
}

greet(name: "Alice")  // 안녕하세요, Alice님!
greet(name: nil)  // 이름이 없습니다.
```

- `guard let`을 사용하면 **조기 종료(early return)** 로 코드 가독성이 향상됨

## 5. 옵셔널 체이닝 (`?.`)

```swift
class Person {
    var pet: String?
}

let user = Person()
print(user.pet?.count)  // nil
```

- `?.`을 사용하여 **nil일 경우 자동으로 실행을 멈춤**

## 6. `??`를 활용한 기본값 설정

```swift
var username: String? = nil
let displayName = username ?? "Guest"
print(displayName)  // Guest
```

- `??` 연산자는 **옵셔널이 `nil`일 경우 기본값을 제공**

## 7. 암시적 옵셔널 (`!`)

```swift
var email: String! = "user@example.com"
print(email)  // user@example.com
```

- `!`를 사용하여 **nil이 아닌 것이 확실한 경우 암시적으로 언래핑**  
- `nil`일 경우 런타임 오류 발생 가능

---

> 옵셔널을 적절히 활용하면 **안전한 코드 작성이 가능하며, 런타임 오류를 방지할 수 있습니다.**

