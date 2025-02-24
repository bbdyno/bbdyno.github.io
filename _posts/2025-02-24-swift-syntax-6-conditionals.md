---
title: 문법 (6) 조건문 (if, switch)
description: Swift에서 제공하는 조건문 if와 switch의 사용법과 예제를 설명합니다.
author: bbdyno
date: 2025-02-24 16:00:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 조건문, if, switch, 흐름 제어]
pin: true
math: true
mermaid: true
---

## 조건문 (if, switch)

Swift에서는 특정 조건에 따라 실행 흐름을 제어할 수 있도록 `if` 문과 `switch` 문을 제공합니다. 이를 활용하면 프로그램의 논리를 명확하게 구성할 수 있습니다.

---

## 1. `if` 문

`if` 문은 조건을 평가하여 참(`true`)이면 코드 블록을 실행합니다.

### 기본 `if` 문 사용

```swift
let temperature = 25

if temperature > 30 {
    print("더운 날씨입니다.")
} else {
    print("적당한 날씨입니다.")
}
```

### `else if`를 활용한 다중 조건

```swift
let score = 85

if score >= 90 {
    print("A 학점입니다.")
} else if score >= 80 {
    print("B 학점입니다.")
} else {
    print("C 학점 이하입니다.")
}
```

---

## 2. `switch` 문

`switch` 문은 여러 개의 경우(case) 중 하나를 선택하여 실행합니다.

### 기본 `switch` 문 사용

```swift
let grade = "B"

switch grade {
case "A":
    print("최고 성적입니다.")
case "B":
    print("좋은 성적입니다.")
case "C":
    print("평균적인 성적입니다.")
default:
    print("기타 성적입니다.")
}
```

### `fallthrough` 키워드 사용

`fallthrough`를 사용하면 다음 case 문까지 실행됩니다.

```swift
let number = 5

switch number {
case 5:
    print("5입니다.")
    fallthrough
case 6:
    print("이 문장도 실행됩니다.")
default:
    print("기타 숫자입니다.")
}
```

### 범위 연산자를 활용한 `switch`

```swift
let age = 25

switch age {
case 0...12:
    print("어린이입니다.")
case 13...19:
    print("청소년입니다.")
case 20...64:
    print("성인입니다.")
default:
    print("노인입니다.")
}
```

---

## 3. `if`와 `switch`의 비교

| 특징        | `if` 문 | `switch` 문 |
|------------|---------|-------------|
| 사용 목적  | 단일 조건 또는 복잡한 조건 | 여러 개의 값 비교 |
| 성능       | 여러 개의 `if-else`가 있을 경우 느려질 수 있음 | 최적화되어 빠르게 동작 |
| 가독성     | 단순한 조건에 적합 | 다중 분기 처리 시 더 직관적 |

---

## 결론

조건문을 적절히 사용하면 코드의 흐름을 명확하게 제어할 수 있습니다.  
- 단순한 조건 판단에는 `if` 문을 사용합니다.  
- 여러 가지 값을 비교해야 한다면 `switch` 문이 더 적합합니다.  

다음 글에서는 **반복문 (`for-in`, `while`, `repeat-while`)**에 대해 알아보겠습니다.
