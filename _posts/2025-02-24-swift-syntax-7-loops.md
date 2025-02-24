---
title: 문법 (7) 반복문 (for-in, while, repeat-while)
description: Swift에서 제공하는 반복문 for-in, while, repeat-while의 개념과 사용법을 설명합니다.
author: bbdyno
date: 2025-02-24 16:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 반복문, for-in, while, repeat-while, 흐름 제어]
pin: true
math: true
mermaid: true
---

## 반복문 (for-in, while, repeat-while)

Swift에서는 **반복문(loop)**을 사용하여 코드 블록을 여러 번 실행할 수 있습니다.  
대표적인 반복문에는 `for-in`, `while`, `repeat-while`이 있습니다.

---

## 1. `for-in` 반복문

`for-in` 문은 컬렉션(Array, Dictionary, Set 등)이나 범위(Range)를 순회할 때 사용됩니다.

### 기본 사용법

```swift
let numbers = [1, 2, 3, 4, 5]

for number in numbers {
    print(number)
}
```

### 범위를 활용한 반복

```swift
for i in 1...5 {
    print("반복 \(i)회")
}
```

### 딕셔너리 순회

```swift
let capitals = ["Korea": "Seoul", "Japan": "Tokyo", "USA": "Washington D.C."]

for (country, capital) in capitals {
    print("\(country)의 수도는 \(capital)입니다.")
}
```

---

## 2. `while` 반복문

`while` 문은 **주어진 조건이 참(`true`)인 동안 반복 실행**합니다.

### 기본 `while` 문 사용

```swift
var count = 3

while count > 0 {
    print("카운트다운: \(count)")
    count -= 1
}
```

### 조건이 변경될 때까지 반복

```swift
var number = 1

while number < 100 {
    number *= 2
    print(number)
}
```

---

## 3. `repeat-while` 반복문

`repeat-while` 문은 **최소 한 번은 실행된 후 조건을 확인**합니다.

### 기본 사용법

```swift
var count = 3

repeat {
    print("반드시 한 번 실행됩니다. 카운트: \(count)")
    count -= 1
} while count > 0
```

---

## 4. 반복문에서 제어문 사용

### `break` - 반복문 종료

```swift
for i in 1...10 {
    if i == 5 {
        break  // 5에서 반복 종료
    }
    print(i)
}
```

### `continue` - 특정 조건에서 다음 반복으로 건너뛰기

```swift
for i in 1...5 {
    if i == 3 {
        continue  // 3을 건너뛰고 다음 반복 실행
    }
    print(i)
}
```

---

## 5. `for-in`, `while`, `repeat-while` 비교

| 특징        | `for-in` | `while` | `repeat-while` |
|------------|---------|---------|---------------|
| 사용 목적  | 컬렉션, 범위 순회 | 조건 기반 반복 | 최소 1회 실행 후 조건 검사 |
| 종료 조건  | 반복 횟수 지정 가능 | 명시적인 조건 필요 | 조건이 `false`일 때 종료 |
| 실행 보장  | 조건에 따라 실행될 수도 안 될 수도 있음 | 조건 만족 시 실행 | 최소 1회 실행 보장 |

---

## 결론

반복문을 활용하면 코드의 가독성과 재사용성을 높일 수 있습니다.  
- 정해진 범위를 반복할 때는 `for-in`을 사용하세요.  
- 조건에 따라 반복 실행할 경우 `while`을 활용하세요.  
- 최소 한 번 실행이 필요한 경우 `repeat-while`이 적합합니다.  

다음 글에서는 **Swift의 함수 (`func`)**에 대해 알아보겠습니다.
