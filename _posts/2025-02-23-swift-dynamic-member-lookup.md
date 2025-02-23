---
title: Swift의 동적 멤버 조회(Dynamic Member Lookup)
description: Swift에서 @dynamicMemberLookup 속성을 사용하여 동적 멤버 조회를 구현하는 방법과 실제 활용 사례를 소개합니다.
author: bbdyno
date: 2025-02-23 14:20:00 +0900
categories: [Programming Language, Swift]
tags: [Dynamic Member Lookup, iOS, iOS 개발, Swift, 개발, 동적멤버조회, 동적타입, 정적타입]
pin: true
math: true
mermaid: true
---

## 1. 정적 타입 언어와 동적 타입 언어란?
프로그래밍 언어는 변수의 타입을 결정하는 방식에 따라 **정적 타입(Static Typing)**과 **동적 타입(Dynamic Typing)**으로 나뉩니다.

### 정적 타입 언어(Static Typing)
정적 타입 언어에서는 변수의 타입이 **컴파일 타임**에 결정되며, 코드 작성 시 명시적으로 타입을 선언해야 합니다. 대표적인 정적 타입 언어로는 Swift, Java, C, C++ 등이 있습니다.

#### 예제 (Swift의 정적 타입 예시)
```swift
var name: String = "Swift"
var age: Int = 31
```
컴파일러는 `name`이 `String` 타입이고, `age`가 `Int` 타입임을 알고 있으며, 다른 타입의 값을 할당하려 하면 오류를 발생시킵니다.

### 동적 타입 언어(Dynamic Typing)
동적 타입 언어에서는 변수의 타입이 **실행 시점**에 결정됩니다. 즉, 변수에 어떤 값이든 할당할 수 있으며, 타입이 변경될 수도 있습니다. 대표적인 동적 타입 언어로는 Python, JavaScript, Ruby 등이 있습니다.

#### 예제 (Python의 동적 타입 예시)
```python
name = "Swift"
name = 31  # 타입 변경 가능
```
이와 같이 `name` 변수는 처음에는 문자열 값을 가지고 있다가 이후 정수 값으로 변경될 수 있습니다.

## 2. 동적 멤버 조회(Dynamic Member Lookup)란?
Swift는 정적 타입 언어로 설계되어 있지만, 특정 상황에서 동적인 기능을 제공할 필요가 있습니다. `@dynamicMemberLookup` 속성은 이러한 요구를 충족시키기 위해 도입된 기능으로, 특정 객체의 멤버(프로퍼티)를 정적으로 선언하지 않고도 **동적으로 접근할 수 있도록 해줍니다**.

이 기능을 활용하면 **컴파일 타임에 존재하지 않는 멤버를 런타임에 조회**하고 처리할 수 있어, JSON 데이터 파싱, 동적 프로퍼티 조회, 스크립팅 언어 인터페이스 등의 구현이 훨씬 유연해집니다.

## 3. `@dynamicMemberLookup` 속성의 역할
Swift의 `@dynamicMemberLookup` 속성은 특정 타입에서 선언된 `subscript(dynamicMember:)` 메서드를 사용하여 **동적 멤버 조회를 가능하게 합니다**. 이 속성이 적용된 타입은 마치 해당 멤버가 존재하는 것처럼 동적으로 값을 검색할 수 있습니다.

### 문법 및 기본 사용법
```swift
@dynamicMemberLookup
struct DynamicStruct {
    private var storage: [String: String]
    
    init(storage: [String: String]) {
        self.storage = storage
    }
    
    subscript(dynamicMember member: String) -> String? {
        return storage[member]
    }
}

let dynamicObject = DynamicStruct(storage: ["name": "Swift", "version": "6.0"])
print(dynamicObject.name)  // "Swift"
print(dynamicObject.version) // "6.0"
```
위 코드에서 `dynamicObject.name` 및 `dynamicObject.version`은 실제로 존재하는 프로퍼티가 아니지만, `subscript(dynamicMember:)`를 통해 동적으로 값을 가져올 수 있습니다.

## 4. 서브스크립트를 사용한 동적 멤버 조회 구현 방법

### 기본 서브스크립트 구현
```swift
@dynamicMemberLookup
struct DynamicDictionary {
    private var data: [String: Any]
    
    init(data: [String: Any]) {
        self.data = data
    }
    
    subscript(dynamicMember member: String) -> Any? {
        return data[member]
    }
}

let jsonData: [String: Any] = ["title": "Swift Programming", "year": 2025]
let book = DynamicDictionary(data: jsonData)
print(book.title)  // "Swift Programming"
print(book.year)   // 2025
```

### 타입 변환을 지원하는 서브스크립트 구현
```swift
@dynamicMemberLookup
struct DynamicTypedDictionary {
    private var data: [String: Any]
    
    init(data: [String: Any]) {
        self.data = data
    }
    
    subscript<T>(dynamicMember member: String) -> T? {
        return data[member] as? T
    }
}

let typedBook = DynamicTypedDictionary(data: jsonData)
let title: String? = typedBook.title
let year: Int? = typedBook.year
print(title ?? "Unknown")  // "Swift Programming"
print(year ?? 0)            // 2025
```

## 5. 동적 멤버 조회의 실제 사용 사례

### JSON 데이터 파싱
```swift
import Foundation

@dynamicMemberLookup
struct JSONWrapper {
    private var json: [String: Any]
    
    init(json: [String: Any]) {
        self.json = json
    }
    
    subscript<T>(dynamicMember member: String) -> T? {
        return json[member] as? T
    }
}

let jsonResponse: [String: Any] = [
    "username": "babydev",
    "followers": 1000
]

let user = JSONWrapper(json: jsonResponse)
print(user.username)  // "babydev"
print(user.followers) // 1000
```

### 키-값 저장소로 활용
```swift
@dynamicMemberLookup
class Settings {
    private var storage: [String: Any] = [:]
    
    subscript<T>(dynamicMember member: String) -> T? {
        return storage[member] as? T
    }
    
    func set<T>(value: T, forKey key: String) {
        storage[key] = value
    }
}

let settings = Settings()
settings.set(value: "Dark", forKey: "theme")
settings.set(value: 24, forKey: "fontSize")

print(settings.theme)   // "Dark"
print(settings.fontSize) // 24
```

## 6. 결론
`@dynamicMemberLookup`은 동적 데이터 처리에 강력한 기능을 제공하지만, **정적 타입 검사의 이점을 잃을 수 있으며, 런타임 오류 가능성이 존재**합니다. JSON 데이터 파싱, 설정 관리 등에 효과적으로 활용할 수 있으며, 적절한 타입 변환을 지원하는 구현을 통해 안정성을 높일 수 있습니다.
