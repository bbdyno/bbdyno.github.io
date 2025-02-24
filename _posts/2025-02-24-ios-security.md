---
title: iOS 앱 보안을 강화하는 방법
description: iOS 앱 개발에서 보안은 필수 요소입니다. 사용자의 데이터를 보호하고, 앱이 해킹되지 않도록 예방하는 것은 매우 중요합니다.
author: bbdyno
date: 2025-02-24 20:03:00 +0900
categories: [Development, iOS]
tags: [ios, Keychain, sqlinjection, SSL, Swift, XSS, 난독화, 네트워크보안, 보안, 앱개발]
pin: true
math: true
mermaid: true
---
iOS 앱 개발에서 보안은 필수 요소입니다. 사용자의 데이터를 보호하고, 앱이 해킹되지 않도록 예방하는 것은 매우 중요합니다.

이 글에서는 iOS 앱 보안을 강화하는 다양한 방법을 예제 코드와 함께 상세히 설명하겠습니다.

## 1. 데이터 보호

### 1) Keychain을 이용한 보안 저장
사용자의 중요한 데이터(토큰, 패스워드 등)는 `UserDefaults`가 아니라 `Keychain`에 저장해야 합니다.

#### (1) Keychain 저장 예제

```swift
import Security

func saveToKeychain(account: String, password: String) {
    let data = password.data(using: .utf8)!
    let query: [String: Any] = [
        kSecClass as String: kSecClassGenericPassword,
        kSecAttrAccount as String: account,
        kSecValueData as String: data
    ]
    SecItemAdd(query as CFDictionary, nil)
}
```

#### (2) Keychain에서 값 가져오기

```swift
func getFromKeychain(account: String) -> String? {
    let query: [String: Any] = [
        kSecClass as String: kSecClassGenericPassword,
        kSecAttrAccount as String: account,
        kSecReturnData as String: kCFBooleanTrue!,
        kSecMatchLimit as String: kSecMatchLimitOne
    ]
    var result: AnyObject?
    SecItemCopyMatching(query as CFDictionary, &result)
    
    guard let data = result as? Data else { return nil }
    return String(data: data, encoding: .utf8)
}
```

## 2. 네트워크 보안

### 1) HTTPS를 강제하는 App Transport Security (ATS)
iOS에서는 HTTPS 사용을 강제합니다. `Info.plist`에 아래 설정을 추가하여 예외 없이 HTTPS를 사용하도록 합니다.

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <false/>
</dict>
```

### 2) SSL Pinning 적용하기
SSL Pinning은 네트워크 공격을 막는 중요한 방법입니다.

#### (1) SSL Pinning 구현 예제

```swift
import Alamofire

class MySessionDelegate: SessionDelegate {
    override func urlSession(
        _ session: URLSession,
        didReceive challenge: URLAuthenticationChallenge,
        completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void
    ) {
        if let serverTrust = challenge.protectionSpace.serverTrust {
            let credential = URLCredential(trust: serverTrust)
            completionHandler(.useCredential, credential)
        } else {
            completionHandler(.cancelAuthenticationChallenge, nil)
        }
    }
}
```

## 3. 사용자 입력 데이터 보호

### 1) SQL Injection 방지
Core Data 또는 SQLite를 사용할 때는 SQL Injection 공격을 방지해야 합니다.

```swift
let query = "SELECT * FROM users WHERE username = ?"
let statement = try db.prepare(query)
statement.bind(username)
```

### 2) XSS (Cross-Site Scripting) 방지
웹뷰를 사용할 때는 JavaScript Injection을 막아야 합니다.

```swift
webView.configuration.preferences.javaScriptEnabled = false
```

## 4. 코드 난독화 및 무결성 보호

### 1) 소스코드 난독화
소스코드를 난독화하면 리버스 엔지니어링을 방지할 수 있습니다. `LLVM Obfuscator`를 사용하는 방법이 있습니다.

```bash
brew install obfuscator-llvm
```

### 2) 앱 변조 감지 (Jailbreak Detection)

```swift
func isJailbroken() -> Bool {
    let paths = [
        "/Applications/Cydia.app",
        "/Library/MobileSubstrate/MobileSubstrate.dylib",
        "/bin/bash"
    ]
    for path in paths {
        if FileManager.default.fileExists(atPath: path) {
            return true
        }
    }
    return false
}
```

---

데이터 보호, 네트워크 보안, 사용자 입력 보호, 코드 난독화 및 무결성 보호 등의 기술을 적용하면 해킹에 강한 앱을 만드는 데에 도움을 줄 수 있습니다.
