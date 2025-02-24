---
title: 문법 (25) Result 타입과 에러 핸들링 개선
description: Swift에서 Result 타입과 에러 핸들링 개선의 개념과 활용법을 설명합니다.
author: bbdyno
date: 2025-02-24 19:30:00 +0900
categories: [Programming Language, Swift]
tags: [Swift, Result Type, Error Handling, 예외 처리, 비동기 처리]
pin: true
math: true
mermaid: true
---

## Result 타입과 에러 핸들링 개선


Swift의 **`Result` 타입**은 **성공과 실패를 명확하게 구분하는 방법**을 제공합니다.  
이를 활용하면 **더 직관적인 에러 처리**가 가능합니다.

## 1. `Result` 타입 기본 사용법

```swift
enum NetworkError: Error {
    case badURL
    case requestFailed
}

func fetchData(from url: String) -> Result<String, NetworkError> {
    guard url == "https://valid.url" else {
        return .failure(.badURL)
    }
    return .success("데이터 로드 성공!")
}
```

- `.success(데이터)` 또는 `.failure(에러)`로 결과를 표현

## 2. `Result` 타입을 활용한 안전한 실행

```swift
let result = fetchData(from: "https://invalid.url")

switch result {
case .success(let data):
    print("데이터: \(data)")
case .failure(let error):
    print("에러 발생: \(error)")
}
```

- `switch` 문을 활용하여 성공과 실패를 명확히 처리

## 3. `Result` 타입과 `map()` 활용

```swift
let transformedResult = result.map { "변환된 데이터: \($0)" }

switch transformedResult {
case .success(let data):
    print(data)
case .failure(let error):
    print("에러 발생: \(error)")
}
```

- `.map()`을 사용하면 **성공 시 데이터를 변환**할 수 있음

## 4. `get()`을 사용한 값 직접 추출

```swift
if let data = try? result.get() {
    print("성공 데이터: \(data)")
} else {
    print("데이터 로드 실패")
}
```

- `get()`을 사용하면 **성공 시 값 추출 가능**, 실패 시 `try?`를 통해 nil 반환

## 5. `Result` 타입을 활용한 비동기 요청

```swift
func fetchUserData(completion: (Result<String, Error>) -> Void) {
    let success = Bool.random()
    if success {
        completion(.success("사용자 데이터 로드 완료"))
    } else {
        completion(.failure(NetworkError.requestFailed))
    }
}

fetchUserData { result in
    switch result {
    case .success(let data):
        print(data)
    case .failure(let error):
        print("에러 발생: \(error)")
    }
}
```

- 비동기 콜백에서도 `Result` 타입을 활용하면 **더 직관적인 처리 가능**

---

> `Result` 타입을 활용하면 **에러 처리의 가독성이 향상되고, 코드의 안정성이 증가**합니다.

