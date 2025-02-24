---
title: 문법 (5) 컬렉션 타입 (Array, Dictionary, Set)
description: Swift에서 제공하는 컬렉션 타입인 배열(Array), 딕셔너리(Dictionary), 집합(Set)의 개념과 사용법을 설명합니다.
author: bbdyno
date: 2025-02-24 15:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, 컬렉션, Array, Dictionary, Set, 데이터 구조]
pin: true
math: true
mermaid: true
---

## 컬렉션 타입

Swift에서는 데이터를 효율적으로 관리하기 위해 `Array`, `Dictionary`, `Set`과 같은 컬렉션 타입을 제공합니다. 각각의 특성을 이해하고 적절하게 활용하면 더욱 효율적인 코드를 작성할 수 있습니다.

---

## 1. 배열 (Array)

배열은 **순서가 있는 데이터의 집합**입니다. Swift에서는 `Array` 타입을 사용하여 배열을 선언할 수 있습니다.

### 배열 생성

```swift
var numbers: [Int] = [1, 2, 3, 4, 5]
var fruits = ["Apple", "Banana", "Cherry"]
```

### 배열 요소 접근

```swift
print(numbers[0]) // 1
print(fruits[1]) // Banana
```

### 배열 요소 추가 및 제거

```swift
fruits.append("Mango")  // 요소 추가
fruits.remove(at: 1)    // 특정 위치의 요소 제거
print(fruits) // ["Apple", "Cherry", "Mango"]
```

### 배열 반복문 활용

```swift
for fruit in fruits {
    print(fruit)
}
```

---

## 2. 딕셔너리 (Dictionary)

딕셔너리는 **키-값(Key-Value) 쌍**으로 데이터를 저장하는 컬렉션입니다.

### 딕셔너리 생성

```swift
var capitals: [String: String] = [
    "Korea": "Seoul",
    "Japan": "Tokyo",
    "USA": "Washington D.C."
]
```

### 값 접근

```swift
print(capitals["Korea"] ?? "Unknown") // Seoul
```

### 값 추가 및 삭제

```swift
capitals["France"] = "Paris"  // 값 추가
capitals.removeValue(forKey: "Japan") // 값 제거
print(capitals)
```

### 딕셔너리 반복문 활용

```swift
for (country, capital) in capitals {
    print("\(country)의 수도는 \(capital)입니다.")
}
```

---

## 3. 집합 (Set)

집합은 **중복이 없는 요소들의 집합**이며, 순서가 없습니다.

### 집합 생성

```swift
var uniqueNumbers: Set<Int> = [1, 2, 3, 3, 2, 1]
print(uniqueNumbers) // {1, 2, 3} (중복이 제거됨)
```

### 집합 연산

```swift
let setA: Set = [1, 2, 3, 4, 5]
let setB: Set = [3, 4, 5, 6, 7]

print(setA.union(setB))       // 합집합 {1, 2, 3, 4, 5, 6, 7}
print(setA.intersection(setB)) // 교집합 {3, 4, 5}
print(setA.subtracting(setB))  // 차집합 {1, 2}
```

---

## 4. 컬렉션 타입 비교

| 특징        | 배열 (Array) | 딕셔너리 (Dictionary) | 집합 (Set) |
|------------|-------------|----------------------|------------|
| 순서      | 유지됨      | 없음                 | 없음       |
| 중복 허용  | 가능        | 키는 중복 불가       | 중복 불가  |
| 요소 접근  | 인덱스로 접근 | 키를 통해 접근      | 포함 여부 체크 |

---

## 결론

Swift의 컬렉션 타입을 이해하면 데이터를 효과적으로 저장하고 관리할 수 있습니다. 배열, 딕셔너리, 집합 각각의 특성을 이해하고 적절한 상황에 맞게 활용하는 것이 중요합니다.

다음 글에서는 **Swift의 제어 흐름 (조건문과 반복문)**에 대해 알아보겠습니다.
