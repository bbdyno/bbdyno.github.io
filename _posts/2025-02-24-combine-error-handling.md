---
title: Combine 프레임워크에서 에러 처리
description: Combine에서의 에러 처리 방식과 주요 개념을 설명합니다.
author: bbdyno
date: 2025-02-24 22:04:03 +0900
categories: [Development, iOS]
tags: [Combine, ios, IOS개발, Swift, swift프로그래밍, 스위프트, 에러처리, 컴바인, 프로그래밍]
pin: true
math: true
mermaid: true
---
Combine 프레임워크는 비동기 프로그래밍을 위한 도구이며, 데이터를 스트림 형태로 처리하는 데 유용합니다.

하지만, 네트워크 통신이나 데이터 처리 과정에서 예기치 않은 에러가 발생할 수 있기 때문에, 이를 적절하게 처리하는 것이 중요합니다.

이 글에서는 Combine에서의 에러 처리 방식과 관련된 주요 개념을 설명하겠습니다.

## 1. Combine에서 에러 처리 개요

Combine 프레임워크에서 `Publisher`는 성공(`Output`)과 실패(`Failure`) 두 가지 결과를 가질 수 있습니다. 실패 시, `Failure` 타입의 에러가 발생하며, 이는 `Publisher`의 타입 파라미터에서 명시됩니다.

```swift
import Combine

enum NetworkError: Error {
    case invalidURL
    case requestFailed
    case decodingError
}

let publisher = Fail<String, NetworkError>(error: .requestFailed)
```

위 코드에서 `Fail`을 사용해 즉시 실패하는 `Publisher`를 생성할 수 있습니다.

## 2. 에러 이벤트를 처리하는 주요 Operator

Combine에서는 에러를 처리하기 위한 다양한 연산자(`Operator`)를 제공합니다. 대표적인 연산자는 다음과 같습니다.

### 2.1 `catch(_:)`

`catch(_:)` 연산자는 에러가 발생했을 때 대체 `Publisher`를 반환하여 에러를 처리합니다.

```swift
let fallbackPublisher = Just("Fallback Value")
    .setFailureType(to: NetworkError.self)

let subscription = publisher
    .catch { _ in fallbackPublisher }
    .sink(receiveCompletion: { completion in
        print(completion)
    }, receiveValue: { value in
        print("Received value: \(value)")
    })
```

출력 결과:

```
Received value: Fallback Value
finished
```

### 2.2 `replaceError(with:)`

에러가 발생하면 특정 값으로 대체하는 방법입니다.

```swift
let subscription = publisher
    .replaceError(with: "Default Value")
    .sink(receiveValue: { value in
        print("Received: \(value)")
    })
```

출력 결과:

```
Received: Default Value
```

### 2.3 `retry(_:)`

에러 발생 시 지정된 횟수만큼 다시 시도하는 연산자입니다.

```swift
let failingPublisher = URLSession.shared.dataTaskPublisher(for: URL(string: "https://invalid.url")!)
    .map { $0.data }
    .decode(type: String.self, decoder: JSONDecoder())
    .retry(3) // 최대 3번까지 재시도
    .eraseToAnyPublisher()
```

## 3. 에러 발생 시 Subscription 자동 취소

`cancel()`을 호출하지 않으면 `Subscription`이 지속될 수 있습니다. `Subscription`을 자동으로 취소하려면 `handleEvents(receiveCompletion:)`을 사용할 수 있습니다.

```swift
let cancellable = publisher
    .handleEvents(receiveCompletion: { completion in
        if case .failure = completion {
            print("Cancelling subscription due to error")
        }
    })
    .sink(receiveCompletion: { _ in }, receiveValue: { _ in })
```

또는 Combine의 `assign(to:on:)`을 활용하면 객체의 라이프사이클과 함께 자동으로 `Subscription`이 해제됩니다.

## 4. Combine과 Result 타입을 활용한 에러 처리

Combine에서 `Result<T, Error>` 타입을 함께 사용하면 보다 명확하게 에러를 전달할 수 있습니다.

```swift
let publisher = Just("Success Value")
    .setFailureType(to: NetworkError.self)
    .map { Result<String, NetworkError>.success($0) }
    .catch { error in Just(Result.failure(error)) }
    .sink(receiveValue: { result in
        switch result {
        case .success(let value):
            print("Success: \(value)")
        case .failure(let error):
            print("Error: \(error)")
        }
    })
```

이 방법을 사용하면, Combine 스트림에서 `Result` 타입을 반환하여 호출부에서 더욱 명확한 에러 핸들링이 가능합니다.

## 마무리

Combine에서의 에러 처리는 `catch`, `replaceError`, `retry` 등의 연산자를 적절히 활용하여 안정적인 스트림을 유지하는 것이 핵심입니다. 또한, `Subscription`을 자동으로 취소하는 방법과 `Result` 타입을 활용한 명확한 에러 처리 기법을 이해하면 더욱 효율적인 비동기 프로그래밍이 가능합니다.
