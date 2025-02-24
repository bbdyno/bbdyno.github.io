---
title: 문법 (24) 에러 처리 (do-try-catch, throws)
description: Swift에서 에러 처리 (do-try-catch, throws)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Error Handling, do-try-catch, throws, 예외 처리]
pin: true
math: true
mermaid: true
---

## 에러 처리 (do-try-catch, throws)


Swift에서는 **에러 처리(Error Handling)** 를 위해 `throws`, `do-try-catch` 구문을 제공합니다.  
이를 활용하면 **안정적인 프로그램 실행**이 가능합니다.

## 1. `throws`를 사용한 에러 정의

```swift
enum LoginError: Error {
    case invalidUsername
    case incorrectPassword
}

func login(username: String, password: String) throws {
    if username != "admin" {
        throw LoginError.invalidUsername
    }
    if password != "password123" {
        throw LoginError.incorrectPassword
    }
    print("로그인 성공!")
}
```

- `Error` 프로토콜을 준수하여 **사용자 정의 에러 타입 생성 가능**

## 2. `do-try-catch`를 사용한 에러 처리

```swift
do {
    try login(username: "user", password: "password123")
} catch LoginError.invalidUsername {
    print("유효하지 않은 사용자 이름입니다.")
} catch LoginError.incorrectPassword {
    print("잘못된 비밀번호입니다.")
} catch {
    print("알 수 없는 오류 발생: \(error)")
}
```

- `do` 블록 내부에서 `try`를 사용하여 **에러를 발생시키는 함수 실행**
- `catch` 블록에서 **다양한 에러 유형을 분기 처리**

## 3. `try?`를 사용한 에러 무시

```swift
if let result = try? login(username: "admin", password: "password123") {
    print("로그인 성공: \(result)")
} else {
    print("로그인 실패")
}
```

- `try?`를 사용하면 **에러 발생 시 nil을 반환**

## 4. `try!`를 사용한 강제 실행 (비추천)

```swift
try! login(username: "admin", password: "password123")  // 정상 실행 시 문제 없음
```

- **에러가 절대 발생하지 않는다고 확신할 때만 사용**
- **실패할 경우 런타임 크래시 발생**

## 5. `rethrows`를 사용한 에러 전파

```swift
func execute(operation: () throws -> Void) rethrows {
    try operation()
}

try execute {
    try login(username: "admin", password: "password123")
}
```

- `rethrows`는 **자신이 직접 에러를 던지지 않고, 전달받은 클로저가 에러를 던질 경우에만 throws 처리**

---

> `do-try-catch`를 활용하면 **안전한 에러 처리가 가능하며, 프로그램의 안정성을 향상**시킬 수 있습니다.

