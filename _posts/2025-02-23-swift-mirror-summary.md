---
title: Swift Mirror 타입 총정리
description: Swift의 Mirror 타입에 대한 개념, 사용법, 예제 및 내부 동작 원리를 공식 문서를 참고하여 정리한 글입니다.
author: bbdyno
date: 2025-02-23 18:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Reflection, Mirror, iOS, 개발]
pin: true
math: true
mermaid: true
---
## 1. `Mirror` 타입 개요
Swift의 `Mirror` 타입은 런타임에 객체의 정보를 조회하는 기능을 제공합니다. 이를 통해 객체의 프로퍼티, 메서드 및 기타 메타데이터를 동적으로 탐색할 수 있습니다.

- **사용 사례**
  - 디버깅 및 로깅
  - 객체의 속성 동적 탐색
  - JSON 직렬화/역직렬화
  - 리플렉션을 활용한 프레임워크 개발

## 2. `Mirror` 타입의 기본 사용법

Swift에서 `Mirror` 타입을 활용하면 객체의 속성을 동적으로 조회할 수 있습니다.

```swift
struct Person {
    let name: String
    let age: Int
}

let person = Person(name: "Alice", age: 30)
let mirror = Mirror(reflecting: person)

for child in mirror.children {
    print("\(child.label ?? "unknown"): \(child.value)")
}
```

**출력:**
```
name: Alice
age: 30
```

## 3. `Mirror` 타입의 주요 프로퍼티 및 메서드

| 프로퍼티/메서드 | 설명 |
|---------------|------|
| `children` | 객체의 모든 프로퍼티를 `Child` 시퀀스로 반환 |
| `subjectType` | 반영된 객체의 타입 반환 |
| `displayStyle` | 구조체, 클래스, 열거형 등의 유형을 반환 |
| `ancestorRepresentation` | 부모 미러의 참조 방식을 반환 |

### `subjectType` 사용 예시

```swift
print("Type: \(mirror.subjectType)") // Type: Person
```

### `displayStyle` 사용 예시
```swift
if let style = mirror.displayStyle {
    print("Display Style: \(style)") // Display Style: struct
}
```

## 4. `Mirror`를 활용한 고급 예제

### 4.1 객체의 모든 프로퍼티를 JSON 형태로 변환하기

```swift
func toJSON(_ object: Any) -> String? {
    let mirror = Mirror(reflecting: object)
    var jsonDict: [String: Any] = [:]
    
    for child in mirror.children {
        if let label = child.label {
            jsonDict[label] = child.value
        }
    }
    
    if let jsonData = try? JSONSerialization.data(withJSONObject: jsonDict, options: .prettyPrinted) {
        return String(data: jsonData, encoding: .utf8)
    }
    
    return nil
}

let jsonString = toJSON(person)
print(jsonString ?? "Serialization failed")
```

### 4.2 클래스 계층 구조에서 `Mirror` 활용

```swift
class Animal {
    var species: String
    init(species: String) {
        self.species = species
    }
}

class Dog: Animal {
    var breed: String
    init(species: String, breed: String) {
        self.breed = breed
        super.init(species: species)
    }
}

let dog = Dog(species: "Canine", breed: "Labrador")
let mirror = Mirror(reflecting: dog)

for child in mirror.children {
    print("\(child.label ?? "unknown"): \(child.value)")
}
```

**출력:**
```
breed: Labrador
species: Canine
```

## 5. `Mirror`의 한계
- **실행 속도**: 정적 타입 확인보다 성능이 느릴 수 있음.
- **비공개 프로퍼티 접근 불가**: `Mirror`는 `private` 속성을 직접 접근할 수 없음.
- **Method 탐색 제한**: 메서드의 리스트를 조회하는 기능이 제공되지 않음.

## 6. 결론
`Mirror`는 Swift에서 객체의 메타데이터를 조회하는 강력한 도구입니다. 디버깅, 동적 속성 접근, JSON 변환 등 다양한 용도로 활용할 수 있습니다. 다만, 성능 이슈 및 접근 제한 사항을 고려하여 적절한 상황에서 사용하는 것이 중요합니다.
