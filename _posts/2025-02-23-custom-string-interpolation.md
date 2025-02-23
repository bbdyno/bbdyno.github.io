---
title: Swift의 Custom String Interpolation 활용하기
description: Swift에서 문자열 보간법을 확장하여 보다 강력한 문자열 포맷팅을 구현하는 방법을 알아봅니다.
author: bbdyno
date: 2025-02-23 14:00:00 +0900
categories: [Programming Language, Swift]
tags: [Apple, iOS, iOS개발, StringInterpolation, Swift, Swift문자열, 문자열, 문자열처리]
pin: true
math: true
mermaid: true
---

## 1. 개요
Swift에서 문자열을 다룰 때 자주 사용되는 기능 중 하나가 **문자열 보간법(String Interpolation)** 입니다. 이는 `"Hello, \(name)!"`과 같은 방식으로 변수를 문자열에 삽입하는 기능입니다. 하지만 기본 제공되는 보간법 외에도 **Custom String Interpolation(사용자 정의 문자열 보간법)** 을 활용하면 보다 강력하고 유연한 문자열 포맷팅이 가능합니다.

이 글에서는 **Swift의 Custom String Interpolation을 이해하고 활용하는 방법**을 예제와 함께 설명하겠습니다.

## 2. 문자열 보간법
기본적으로 Swift의 문자열 보간법은 `\()` 구문을 사용하여 변수를 문자열 안에 포함할 수 있도록 해줍니다. 예제를 살펴보겠습니다.

```swift
let name = "Alice"
let age = 25
print("My name is \(name) and I am \(age) years old.")
```

출력 결과:

```
My name is Alice and I am 25 years old.
```

하지만 특정한 포맷을 적용하고 싶다면, 기본적인 보간법으로는 한계가 있습니다. 예를 들어, **날짜 형식 변환, 숫자 포맷 지정, JSON 변환** 등의 작업을 할 때는 추가적인 처리가 필요합니다. 이를 해결하기 위해 **Custom String Interpolation** 을 활용할 수 있습니다.

## 3. Custom String Interpolation이란?
**Custom String Interpolation** 이란 `String.StringInterpolation` 프로토콜을 확장하여, 원하는 형태의 보간법을 직접 정의하는 기능입니다. 이를 통해 문자열 보간 시 특정한 형식을 자동으로 적용할 수 있습니다.

### 3.1 Custom String Interpolation 구현 방법
사용자 정의 보간법을 추가하려면 `StringInterpolation` 프로토콜을 확장하여 `appendInterpolation` 메서드를 구현하면 됩니다.

기본 구조는 다음과 같습니다:

```swift
extension String.StringInterpolation {
    mutating func appendInterpolation(커스텀_메서드_이름: 타입) {
        // 변환 및 포맷팅 처리
        appendLiteral(변환된 문자열)
    }
}
```

다음으로, 예제를 살펴보겠습니다.

## 4. Custom String Interpolation 예제

### 4.1 숫자 포맷 적용하기
숫자를 보간할 때, 특정한 자리수를 맞추거나 통화 형식으로 변환하는 경우가 많습니다. 이를 위해 `NumberFormatter`를 활용한 Custom String Interpolation을 만들어보겠습니다.

```swift
import Foundation

extension String.StringInterpolation {
    mutating func appendInterpolation(_ value: Double, format: String) {
        let formatter = NumberFormatter()
        formatter.numberStyle = .decimal
        formatter.minimumFractionDigits = 2
        formatter.maximumFractionDigits = 2
        let formattedValue = formatter.string(from: NSNumber(value: value)) ?? "\(value)"
        appendLiteral(formattedValue)
    }
}

let price = 1234.5
print("Price: \(price, format: \"%.2f\")")
```

출력 결과:

```
Price: 1,234.50
```

### 4.2 날짜 포맷 변환하기
Swift의 `Date` 타입은 기본적으로 사람이 읽기 어려운 형식으로 출력됩니다. 이를 가독성이 좋은 포맷으로 변환하는 보간법을 추가해보겠습니다.

```swift
import Foundation

extension String.StringInterpolation {
    mutating func appendInterpolation(_ date: Date, format: String) {
        let formatter = DateFormatter()
        formatter.dateFormat = format
        let formattedDate = formatter.string(from: date)
        appendLiteral(formattedDate)
    }
}

let now = Date()
print("Current Date: \(now, format: \"yyyy-MM-dd HH:mm\")")
```

출력 결과:

```
Current Date: 2025-02-19 14:30
```

### 4.3 JSON 문자열 변환
Swift의 `Codable` 타입을 JSON 문자열로 변환하는 경우, `JSONEncoder`를 사용해야 합니다. 이 과정을 자동화하는 보간법을 만들어보겠습니다.

```swift
import Foundation

extension String.StringInterpolation {
    mutating func appendInterpolation<T: Encodable>(_ value: T) {
        let encoder = JSONEncoder()
        encoder.outputFormatting = .prettyPrinted
        if let jsonData = try? encoder.encode(value), let jsonString = String(data: jsonData, encoding: .utf8) {
            appendLiteral(jsonString)
        } else {
            appendLiteral("(Invalid JSON)")
        }
    }
}

struct User: Codable {
    let name: String
    let age: Int
}

let user = User(name: "Alice", age: 25)
print("User Data: \(user)")
```

출력 결과:

```
User Data: {
  "name": "Alice",
  "age": 25
}
```

## 5. 정리
Custom String Interpolation을 활용하면 보다 **직관적이고 가독성이 높은 문자열 변환**이 가능합니다. 위에서 살펴본 예제처럼 **숫자, 날짜, JSON 변환 등 다양한 활용**이 가능하며, 이를 적용하면 코드의 유지보수성과 가독성이 향상됩니다.

### Custom String Interpolation을 사용할 때의 장점
✅ 코드 가독성이 증가  
✅ 불필요한 변환 코드 제거  
✅ 일관된 포맷 유지  
✅ 확장 가능성 증가

### Custom String Interpolation을 사용할 때의 주의점
⚠️ 성능 오버헤드 고려 (특히 `JSONEncoder`, `DateFormatter` 등 사용 시)  
⚠️ 사용 목적에 맞는 보간법 설계 필요

Swift의 Custom String Interpolation을 활용하여 더욱 **효율적이고 가독성 좋은 코드**를 작성해보세요!
