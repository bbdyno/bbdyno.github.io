---
title: Swift Concurrency 개념과 사용법 총정리
description: Swift Concurrency의 개념과 사용법을 공식 문서를 기반으로 정리합니다.
author: bbdyno
date: 2025-02-23 18:11:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Concurrency, async-await, actor, structured-concurrency]
pin: true
math: true
mermaid: true
---
Swift 5.5부터 도입된 **Swift Concurrency**는 비동기 프로그래밍을 더욱 안전하고 효율적으로 처리할 수 있도록 설계된 시스템입니다. 공식 문서인 [Swift.org](https://www.swift.org)와 [Apple Developer Documentation](https://developer.apple.com/documentation/swift)에서 제공하는 자료를 기반으로 Swift Concurrency의 개념과 사용법을 정리합니다.

## 1. Swift Concurrency 개요

Swift Concurrency는 주요 개념인 `async`/`await`, `Task`, `actor`, `structured concurrency` 등을 포함합니다. 이를 통해 코드의 가독성을 높이고, 메모리 안전성을 보장하며, 경쟁 상태(race condition)를 최소화할 수 있습니다.

### 주요 특징
- `async` / `await` 기반의 비동기 프로그래밍
- Task 기반의 동시성 모델 지원
- `actor`를 활용한 데이터 경쟁 방지
- Structured Concurrency를 통한 안정적인 작업 관리

## 2. async / await

### 2.1 async 키워드

Swift에서 `async` 키워드는 해당 함수가 비동기적으로 실행된다는 것을 의미합니다.

```swift
func fetchData() async -> String {
    return "데이터 로드 완료"
}
```

### 2.2 await 키워드

`await` 키워드는 `async` 함수 내에서 비동기 작업이 완료될 때까지 기다리는 역할을 합니다.

```swift
func performTask() async {
    let result = await fetchData()
    print(result)
}
```

## 3. Task와 TaskGroup

Swift에서는 비동기 작업을 `Task`를 이용해 실행할 수 있으며, 여러 작업을 그룹화하는 `TaskGroup`도 제공합니다.

```swift
Task {
    let data = await fetchData()
    print(data)
}
```

### TaskGroup 예제

```swift
func processMultipleTasks() async {
    await withTaskGroup(of: String.self) { group in
        group.addTask {
            return "작업 1 완료"
        }
        group.addTask {
            return "작업 2 완료"
        }

        for await result in group {
            print(result)
        }
    }
}
```

## 4. Actor

Actor는 데이터 경쟁을 방지하고, 공유된 상태를 보호하는 역할을 합니다.

```swift
actor Counter {
    private var value: Int = 0

    func increment() {
        value += 1
    }

    func getValue() -> Int {
        return value
    }
}
```

## 5. Structured Concurrency

Structured Concurrency를 활용하면 부모 Task 내에서 자식 Task를 안전하게 관리할 수 있습니다.

```swift
func fetchImages() async throws -> [UIImage] {
    try await withThrowingTaskGroup(of: UIImage.self) { group in
        var images: [UIImage] = []

        for url in imageUrls {
            group.addTask {
                return try await loadImage(from: url)
            }
        }

        for try await image in group {
            images.append(image)
        }

        return images
    }
}
```

## 6. 공식 문서 참고
- [Swift.org - Concurrency](https://www.swift.org/swift-book/LanguageGuide/Concurrency.html)
- [Apple Developer - Swift Concurrency](https://developer.apple.com/documentation/swift/swift_concurrency)
