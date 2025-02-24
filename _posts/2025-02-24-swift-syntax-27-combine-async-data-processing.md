---
title: 문법 (27) Combine (비동기 데이터 처리)
description: Swift에서 Combine (비동기 데이터 처리)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Combine, 비동기, Reactive Programming, 데이터 스트림]
pin: true
math: true
mermaid: true
---

## Combine (비동기 데이터 처리)


Swift의 **Combine 프레임워크**는 **비동기 이벤트를 선언적으로 처리할 수 있도록 도와주는 API**입니다.  
이를 활용하면 **데이터 스트림을 조합하고 변형하여 효율적인 비동기 프로그래밍**이 가능합니다.

## 1. Combine의 핵심 개념

Combine은 **퍼블리셔(Publisher)** 와 **구독자(Subscriber)** 의 개념을 기반으로 동작합니다.

- **Publisher**: 데이터를 발행 (예: `Just`, `URLSession.DataTaskPublisher`)
- **Subscriber**: 데이터를 받아 처리 (예: `sink`, `assign`)
- **Operator**: 데이터 변환 및 조작 (예: `map`, `filter`)

## 2. 기본적인 `Just` 사용

```swift
import Combine

let publisher = Just("Hello, Combine!")

let subscription = publisher.sink { value in
    print(value)
}
```

- `Just`는 **단일 값을 방출하는 퍼블리셔**

## 3. `map`을 활용한 데이터 변환

```swift
import Combine

let publisher = Just(5)
    .map { $0 * 2 }

let subscription = publisher.sink { value in
    print(value)  // 10
}
```

- `map` 연산자를 활용하여 **데이터 변환 가능**

## 4. 네트워크 요청 처리 (`URLSession` + Combine)

```swift
import Combine
import Foundation

let url = URL(string: "https://jsonplaceholder.typicode.com/todos/1")!

let publisher = URLSession.shared.dataTaskPublisher(for: url)
    .map { $0.data }
    .decode(type: [String: Any].self, decoder: JSONDecoder())

let subscription = publisher.sink(receiveCompletion: { completion in
    print(completion)
}, receiveValue: { data in
    print(data)
})
```

- `dataTaskPublisher`를 사용하여 **비동기 네트워크 요청 처리 가능**

## 5. `filter`를 활용한 값 필터링

```swift
import Combine

let numbers = [1, 2, 3, 4, 5].publisher

let subscription = numbers
    .filter { $0 % 2 == 0 }
    .sink { print($0) }

// 출력: 2, 4
```

- `filter` 연산자를 사용하여 **짝수만 출력**

## 6. `combineLatest`를 활용한 여러 퍼블리셔 결합

```swift
import Combine

let publisher1 = Just("Hello")
let publisher2 = Just("Swift")

let subscription = publisher1
    .combineLatest(publisher2)
    .sink { print("\($0) \($1)") }

// 출력: Hello Swift
```

- `combineLatest`를 사용하면 **여러 퍼블리셔의 최신 값 결합 가능**

## 7. `assign`을 활용한 상태 업데이트

```swift
import Combine

class ViewModel {
    @Published var text = "Initial"

    var subscription: AnyCancellable?

    init() {
        subscription = Just("Updated")
            .assign(to: &$text)
    }
}

let viewModel = ViewModel()
print(viewModel.text)  // Updated
```

- `assign(to:)`를 사용하여 **데이터를 특정 프로퍼티에 자동 업데이트**

---

> Combine을 활용하면 **비동기 데이터 흐름을 더 쉽게 관리하고, 코드의 가독성을 높일 수 있습니다.**

