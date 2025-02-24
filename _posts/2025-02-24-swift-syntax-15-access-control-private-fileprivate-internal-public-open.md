---
title: 문법 (15) 접근 제어 (private, fileprivate, internal, public, open)
description: Swift에서 접근 제어 (private, fileprivate, internal, public, open)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Access Control, Private, Fileprivate, Internal, Public, Open]
pin: true
math: true
mermaid: true
---

## 접근 제어 (private, fileprivate, internal, public, open)


Swift에서는 **접근 제어(Access Control)** 를 통해 코드의 가시성을 제어할 수 있습니다.  
이를 활용하면 **캡슐화(Encapsulation)** 를 강화하고, 코드의 안전성을 높일 수 있습니다.

## 1. 접근 제어자 개요

| 접근 제어자  | 설명 |
|------------|------------------------------------------------|
| `private`  | 같은 파일 내에서도 해당 **클래스/구조체 내부에서만** 접근 가능 |
| `fileprivate` | 같은 파일 내에서만 접근 가능 |
| `internal` | 같은 모듈 내에서 접근 가능 (기본값) |
| `public` | 외부 모듈에서도 접근 가능 (상속 가능, 오버라이딩 제한) |
| `open` | 외부 모듈에서도 접근 가능 (상속 가능, 오버라이딩 가능) |

## 2. `private` 예제

```swift
class User {
    private var password: String = "1234"

    func checkPassword(input: String) -> Bool {
        return input == password
    }
}

let user = User()
// print(user.password) // 오류 발생 (private 접근 불가)
print(user.checkPassword(input: "1234")) // true
```

- `password` 프로퍼티는 클래스 내부에서만 접근 가능

## 3. `fileprivate` 예제

```swift
class Logger {
    fileprivate func logMessage(_ message: String) {
        print("LOG: \(message)")
    }
}

let logger = Logger()
logger.logMessage("파일 내에서 호출 가능") // 정상 동작
```

- 같은 파일 내에서는 `fileprivate` 메서드에 접근 가능

## 4. `internal` 예제

```swift
struct Profile {
    internal var nickname = "SwiftDev"
}

let profile = Profile()
print(profile.nickname) // 같은 모듈 내에서는 접근 가능
```

- `internal`은 기본 접근 수준이며, 같은 모듈 내에서 접근 가능

## 5. `public` vs `open` 차이점

```swift
public class PublicClass {
    public var name = "Public Name"
}

open class OpenClass {
    open var name = "Open Name"
}
```

- `public` 클래스는 외부에서 접근 가능하지만, **상속 후 오버라이딩 제한**
- `open` 클래스는 외부에서 **상속 및 오버라이딩 가능**

## 6. 접근 제어자를 활용한 코드 보호

- **외부에서 접근할 필요 없는 속성은 `private` 사용**
- **같은 파일에서만 접근할 경우 `fileprivate` 고려**
- **기본적으로 `internal`을 유지하고 필요할 때 `public`을 사용**
- **라이브러리를 설계할 때 `public`과 `open`을 적절히 활용**

---

> 접근 제어를 활용하면 **코드의 보안성을 높이고, 예기치 않은 변경을 방지**할 수 있습니다.

