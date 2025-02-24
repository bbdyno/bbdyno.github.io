---
title: 문법 (3) 변수와 상수 (var, let)
description: Swift에서 변수(var)와 상수(let)의 개념과 차이를 설명합니다.
author: bbdyno
date: 2025-02-24 14:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 변수, 상수, var, let, 데이터 저장]
pin: true
math: true
mermaid: true
---

## 변수와 상수

Swift에서는 데이터를 저장할 때 **변수(var)**와 **상수(let)**을 사용합니다. 이 두 개념을 이해하는 것은 Swift 프로그래밍의 기초이며, 메모리 관리와 코드의 안정성에 큰 영향을 미칩니다.

---

## 1. 변수 (`var`)

변수는 값이 변경될 수 있는 저장소입니다. `var` 키워드를 사용하여 변수를 선언할 수 있습니다.

```swift
var name = "Swift"
print(name) // Swift

name = "iOS Development"
print(name) // iOS Development
```

위 코드에서 `name` 변수는 `"Swift"`로 초기화된 후 `"iOS Development"`로 변경되었습니다.

---

## 2. 상수 (`let`)

상수는 한 번 값을 할당하면 변경할 수 없습니다. `let` 키워드를 사용하여 선언합니다.

```swift
let language = "Swift"
print(language) // Swift

// language = "Python" // 오류 발생: 상수는 변경할 수 없음
```

`let`을 사용하면 의도치 않은 값 변경을 방지할 수 있어 코드의 안정성이 높아집니다.

---

## 3. 변수와 상수의 차이

| 특징      | 변수 (`var`) | 상수 (`let`) |
|----------|------------|------------|
| 값 변경 가능 여부 | 가능 | 불가능 |
| 코드 안정성 | 상대적으로 낮음 | 높음 |
| 사용 예 | 동적으로 변하는 값 | 고정된 값 |

---

## 4. 변수와 상수의 타입 명시

Swift는 타입 추론 기능을 제공하지만, 명확한 코드 작성을 위해 타입을 명시할 수도 있습니다.

```swift
var age: Int = 25
let pi: Double = 3.14159
let isSwiftFun: Bool = true
```

---

## 5. 실전 활용 예제

### 사용자 프로필 관리 (변수)

```swift
var username = "bbdyno"
var followers = 100

followers += 50 // 새로운 팔로워 추가
print("\(username)의 팔로워 수: \(followers)")
```

### 변하지 않는 정보 (상수)

```swift
let maxLoginAttempts = 3
var currentLoginAttempt = 0

while currentLoginAttempt < maxLoginAttempts {
    print("로그인 시도 \(currentLoginAttempt + 1)")
    currentLoginAttempt += 1
}
```

---

## 6. 변수와 상수 활용에 대한 Best Practice

- **변경될 가능성이 없는 값은 항상 `let`을 사용** (안정성 증가)
- **반드시 필요한 경우에만 `var`을 사용** (메모리 관리 최적화)
- **타입 추론을 활용하되, 명확한 타입을 지정하는 것이 가독성에 유리**

---

## 결론

Swift에서 변수(`var`)와 상수(`let`)를 적절하게 활용하면 코드의 안정성과 유지보수성이 향상됩니다. 가능하면 **불변성을 유지하는 방향**으로 코드를 작성하는 것이 바람직합니다.

다음 글에서는 **Swift의 데이터 타입**에 대해 알아보겠습니다.
