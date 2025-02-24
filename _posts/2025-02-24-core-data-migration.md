---
title: Core Data를 활용한 Data Migration
description: Core Data에서 데이터 모델 변경 시 활용할 수 있는 마이그레이션 방법에 대해 알아봅니다.
author: bbdyno
date: 2025-02-24 21:52:00 +0900
categories: [Development, iOS]
tags: [CoreData, datamigration, ios, Swift, 개발, 데이터마이그레이션, 마이그레이션, 코어데이터]
pin: true
math: true
mermaid: true
---
Core Data에서는 경량 마이그레이션(Lightweight Migration)과 무거운 마이그레이션(Heavyweight Migration)을 지원하며, 복잡한 마이그레이션을 위해 매핑 모델(Mapping Model)을 활용할 수도 있습니다.

## 1. Core Data 마이그레이션 개요
데이터 모델이 변경되면 기존 데이터를 새로운 데이터 모델에 맞게 변환해야 합니다. Core Data는 이를 자동으로 처리할 수 있도록 다양한 마이그레이션 전략을 제공합니다.

### 마이그레이션이 필요한 상황
- 엔터티(Entity) 이름 변경
- 속성(Attribute) 추가/삭제
- 관계(Relationship) 변경
- 데이터 변환이 필요한 경우

## 2. 경량 마이그레이션 (Lightweight Migration)
경량 마이그레이션은 Core Data가 자동으로 수행하는 방식으로, 비교적 단순한 변경 사항이 있을 때 사용됩니다.

### 지원하는 변경 사항
- 속성 추가 및 삭제
- 속성의 기본값 변경
- 관계 추가 및 삭제
- 엔터티 추가

### 설정 방법
#### 1) 새로운 데이터 모델 버전 생성
Xcode에서 `.xcdatamodeld` 파일을 열고 **Editor > Add Model Version**을 선택하여 새로운 모델 버전을 추가합니다.

#### 2) 코드에서 자동 마이그레이션 활성화

```swift
let container = NSPersistentContainer(name: "MyApp")
if let description = container.persistentStoreDescriptions.first {
    description.shouldMigrateStoreAutomatically = true
    description.shouldInferMappingModelAutomatically = true
}

container.loadPersistentStores { (storeDescription, error) in
    if let error = error {
        fatalError("Unresolved error \(error)")
    }
}
```

이렇게 설정하면 Core Data가 변경 사항을 자동으로 감지하고 데이터를 마이그레이션합니다.

## 3. 무거운 마이그레이션 (Heavyweight Migration)
경량 마이그레이션이 불가능한 경우 수동 마이그레이션을 수행해야 합니다. 예를 들어 다음과 같은 변경 사항이 있는 경우 무거운 마이그레이션이 필요합니다.

### 무거운 마이그레이션이 필요한 경우
- 속성 타입 변경
- 엔터티 이름 변경
- 복잡한 데이터 변환

### 수동 마이그레이션 과정

#### 1) 매핑 모델(Mapping Model) 생성
**File > New > Data Model Mapping Model**을 선택하여 `.xcmappingmodel` 파일을 생성합니다.

#### 2) 매핑 규칙 정의
매핑 모델에서 소스 모델과 타겟 모델을 설정하고 속성 변환 규칙을 추가합니다.

#### 3) 마이그레이션 코드 작성

```swift
let sourceURL = URL(fileURLWithPath: "sourcePath")
let destinationURL = URL(fileURLWithPath: "destinationPath")

if let mappingModel = NSMappingModel(from: nil, forSourceModel: sourceModel, destinationModel: destinationModel) {
    let migrationManager = NSMigrationManager(sourceModel: sourceModel, destinationModel: destinationModel)

    do {
        try migrationManager.migrateStore(
            from: sourceURL,
            sourceType: NSSQLiteStoreType,
            options: nil,
            with: mappingModel,
            toDestinationURL: destinationURL,
            destinationType: NSSQLiteStoreType,
            destinationOptions: nil
        )
    } catch {
        print("Migration failed: \(error)")
    }
}
```

## 4. 데이터 마이그레이션 시 발생할 수 있는 문제와 해결 방법

### 마이그레이션 실패 원인 및 해결 방법

| 실패 원인 | 해결 방법 |
|-----------|----------|
| 모델 변경 감지 실패 | `shouldInferMappingModelAutomatically = true` 설정 |
| SQLite 파일 손상 | 기존 스토어 삭제 후 새로 생성 또는 백업 후 복원 |
| 매핑 모델 오류 | `.xcmappingmodel` 수정 및 매핑 규칙 재정의 |

## 5. 예제 프로젝트

### 경량 마이그레이션 예제
데이터 모델이 다음과 같이 변경되었다고 가정합니다.

#### 변경 전:
```swift
class User: NSManagedObject {
    @NSManaged var name: String
}
```

#### 변경 후:
```swift
class User: NSManagedObject {
    @NSManaged var name: String
    @NSManaged var age: Int16
}
```

위처럼 속성을 추가하는 변경은 경량 마이그레이션으로 처리할 수 있습니다. Xcode에서 자동으로 마이그레이션을 활성화하면 됩니다.

### 무거운 마이그레이션 예제
만약 `name` 속성의 타입이 `String`에서 `Data`로 변경되었다면 무거운 마이그레이션이 필요합니다.

이 경우 매핑 모델을 사용하여 변환해야 합니다.

#### 변환 로직 구현:
```swift
class NameToDataTransformer: ValueTransformer {
    override func transformedValue(_ value: Any?) -> Any? {
        guard let string = value as? String else { return nil }
        return string.data(using: .utf8)
    }
}
```

이제 `NSMigrationManager`를 사용하여 직접 마이그레이션을 실행할 수 있습니다.

## 마무리
Core Data 마이그레이션은 데이터 모델이 변경될 때 필수적으로 고려해야 하는 요소입니다. 경량 마이그레이션은 자동으로 수행되지만, 복잡한 변경이 있는 경우 무거운 마이그레이션을 수행해야 합니다. 특히 매핑 모델을 활용하면 보다 세밀한 마이그레이션이 가능합니다.

데이터 모델 변경이 필요할 경우 미리 마이그레이션 전략을 수립하여 원활한 데이터 이전을 보장하는 것이 중요합니다.
