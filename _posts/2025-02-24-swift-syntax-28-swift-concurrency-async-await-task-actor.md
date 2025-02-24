---
title: 문법 (28) Swift Concurrency (async/await, Task, actor)
description: Swift에서 Swift Concurrency (async/await, Task, actor)의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Concurrency, async await, Task, actor, 비동기]
pin: true
math: true
mermaid: true
---

## Swift Concurrency (async/await, Task, actor)


Swift의 **Concurrency(동시성)** 기능은 비동기 프로그래밍을 더욱 안전하고 직관적으로 만들기 위해 도입되었습니다.  
이를 통해 `async/await`, `Task`, `actor`를 활용하여 **코드를 더 쉽게 관리할 수 있습니다.**

## 1. `async`와 `await` 기본 개념

```swift
func fetchData() async -> String {
    return "데이터 로드 완료"
}

Task {
    let result = await fetchData()
    print(result)
}
```

- `async` 키워드를 사용하여 **비동기 함수 선언**
- `await`을 사용하여 **비동기 함수의 실행 결과를 기다림**

## 2. `Task`를 활용한 비동기 실행

```swift
Task {
    let first = await fetchData()
    print(first)
}
```

- `Task` 블록을 활용하여 **비동기 코드 실행**

## 3. `TaskGroup`을 활용한 병렬 처리

```swift
import Foundation

func performParallelTasks() async {
    await withTaskGroup(of: String.self) { group in
        for i in 1...3 {
            group.addTask {
                return "작업 \(i) 완료"
            }
        }

        for await result in group {
            print(result)
        }
    }
}

Task {
    await performParallelTasks()
}
```

- `TaskGroup`을 사용하여 **여러 개의 비동기 작업을 동시에 실행 가능**

## 4. `actor`를 활용한 스레드 안전성

```swift
actor Counter {
    private var value = 0

    func increment() {
        value += 1
    }

    func getValue() -> Int {
        return value
    }
}

let counter = Counter()

Task {
    await counter.increment()
    print(await counter.getValue())
}
```

- `actor`는 **멀티스레드 환경에서도 안전한 데이터 접근을 보장**

## 5. `@MainActor`를 활용한 UI 업데이트

```swift
import SwiftUI

@MainActor
class ViewModel: ObservableObject {
    @Published var message = ""

    func fetchData() async {
        await Task.sleep(2 * 1_000_000_000) // 2초 지연
        message = "데이터 업데이트 완료"
    }
}

let viewModel = ViewModel()

Task {
    await viewModel.fetchData()
    print(viewModel.message)
}
```

- `@MainActor`를 사용하여 **UI 업데이트를 메인 스레드에서 안전하게 실행**

---

> Swift Concurrency를 활용하면 **보다 간결하고 직관적인 비동기 프로그래밍이 가능**합니다.

