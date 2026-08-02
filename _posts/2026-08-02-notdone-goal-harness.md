---
title: "검증된 상태를 다음 작업으로 넘기는 법: NotDone Goal Harness"
description: "Goal Plan, JIT 계약, 제한된 재시도, Verified Workspace Baseline과 Human Evidence Queue로 구성한 NotDone v0.2의 내부 구조"
author: bbdyno
date: 2026-08-02 15:00:00 +0900
categories: [Development, AI Agent]
tags: [NotDone, Goal Plan, Agent Harness, Verification, Workspace Baseline, Human Evidence]
pin: false
math: false
mermaid: true
---

[앞 글]({% post_url 2026-08-02-notdone-development-story %})에서는 AI 코딩 에이전트의 완료 문장과 완료 증거를 분리하게 된 배경을 살펴봤습니다.

이번 글에서는 NotDone v0.2.0이 여러 Task를 어떤 상태 모델로 실행하는지 기술적으로 정리합니다. 핵심은 더 긴 프롬프트가 아니라, **기계가 읽고 다시 시작할 수 있는 목표 프로토콜**입니다.

## Markdown은 설명이고 JSON Schema가 프로토콜입니다

사람이 읽는 작업 목록은 목표를 이해하기에 좋습니다. 하지만 `READY`, `DONE` 같은 단어만으로 실행 상태를 결정하면 작성자의 표현 방식에 따라 결과가 달라집니다.

NotDone은 JSON Schema를 portable protocol의 원본으로 사용합니다. 기본 Goal Plan은 `.notdone/goal.json`이며 다음 정보를 고정합니다.

| 필드 | 역할 |
|---|---|
| `taskId` | Task를 식별하는 안정적인 ID |
| `dependsOn` | 먼저 PASS해야 하는 Task |
| `acceptanceCriteria` | 완료 주장과 실행할 check |
| `allowedPaths` | 에이전트가 변경할 수 있는 경로 |
| `requiredPaths` | 반드시 변경돼야 하는 경로 |
| `forbiddenPaths` | 변경하면 안 되는 경로 |
| `requiredVerifiers` | 자동 검증 gate |
| `humanEvidenceRequirements` | 사람이 확인해야 할 항목 |
| `retryPolicy` | 재시도 횟수와 허용 실패 |
| `completionPolicy` | 전체 완료 판정 규칙 |

다음은 공식 예제의 핵심 부분을 줄인 형태입니다.

~~~json
{
  "schemaVersion": "1.0",
  "id": "goal.example",
  "title": "Ship a verified feature",
  "execution": {
    "adapter": "codex-exec",
    "timeoutMs": 1800000,
    "externalNetwork": "allow"
  },
  "tasks": [
    {
      "taskId": "feature",
      "acceptanceCriteria": [
        {
          "id": "criterion.feature-tests",
          "statement": "The feature test passes.",
          "checks": [
            {
              "id": "check.feature-tests",
              "type": "command",
              "command": "pnpm test",
              "expect": { "exitCode": 0 }
            }
          ]
        }
      ],
      "allowedPaths": ["src/**", "test/**"],
      "forbiddenPaths": ["secrets/**"],
      "retryPolicy": {
        "maxAttempts": 3,
        "retryOn": ["execution-failed", "verification-failed"]
      }
    }
  ]
}
~~~

실제 문서에는 생략한 필수 필드까지 모두 들어가야 합니다. `notdone goal validate`는 이 JSON이 Goal Plan Schema와 일치하는지, 필드와 ID 및 의존성 목록의 형식이 올바른지 실행 전에 검사합니다. 존재하지 않는 Task를 의존성으로 적는 것 같은 의미 오류는 실행 전 리뷰에서도 함께 확인해야 합니다.

## Goal Orchestrator의 전체 루프

Goal Orchestrator는 다음 Task를 찾는 것에서 끝나지 않습니다. 현재 상태가 저장된 보고서와 일치하는지 먼저 확인하고, 실행 결과에 따라 다음 상태를 결정합니다.

~~~mermaid
stateDiagram-v2
    [*] --> Reconcile
    Reconcile --> SelectReady
    SelectReady --> FreezeRequest
    FreezeRequest --> Execute
    Execute --> Verify
    Verify --> SaveBaseline: PASS
    SaveBaseline --> SelectReady
    Verify --> Retry: 허용된 실패
    Retry --> FreezeRequest
    Verify --> HumanQueue: 사람 증거 필요
    HumanQueue --> Resume: 증거 제출과 attest
    Resume --> Verify
    Verify --> Blocked: 모호함 또는 금지 변경
    SelectReady --> Complete: 모든 Task VERIFIED
~~~

이 루프는 `goal run`과 `goal resume`이 공유합니다. 프로세스가 중간에 종료돼도 메모리 속 추측으로 이어서 실행하지 않습니다. 저장된 state와 실제 VerificationReport를 다시 맞춘 뒤 계속합니다.

### 1. State Reconciler

state 파일에 `RUNNING`이라고 적혀 있어도 실제 보고서가 PASS라면 Task를 `VERIFIED`로 복구합니다. 반대로 완료 상태를 뒷받침하는 보고서가 없으면 텍스트만 믿고 진행하지 않습니다.

재시작 가능한 하네스에서 state는 진실 그 자체가 아니라, 증거 저장소를 빠르게 읽기 위한 투영 값에 가깝습니다.

### 2. READY Task 선택

Task는 자신의 모든 `dependsOn`이 `VERIFIED`일 때만 선택됩니다. 단순 배열 순서나 Markdown 체크박스를 실행 근거로 사용하지 않습니다.

### 3. JIT Task Compiler

선택한 Task Template은 실행 직전에 불변 request로 컴파일됩니다. 여기에는 다음 요소가 결합됩니다.

- 고정된 acceptance criteria
- 자동 verifier gate
- human evidence gate
- allowed, required, forbidden path
- 실행 직전 preflight baseline
- 검증 기준이 되는 verification baseline
- retry라면 이전 실패 run과 이유

request는 한 번 기록된 뒤 다른 내용으로 덮어쓸 수 없습니다. retry가 필요하면 같은 request를 수정하는 대신 새로운 run ID와 attempt를 만듭니다.

## 두 종류의 baseline

NotDone 실행에는 역할이 다른 두 baseline이 있습니다.

### Preflight Baseline

에이전트를 실행하기 직전의 작업 공간 상태입니다. 실행 이후 무엇이 달라졌는지 계산할 때 사용합니다.

### Verification Baseline

현재 Task가 신뢰하고 시작해야 하는 상태입니다. 첫 Task에서는 목표 시작 시점의 baseline이고, 후속 Task에서는 이전 Task가 PASS한 successor baseline입니다.

~~~mermaid
flowchart LR
    G["Git base revision"] --> B["Verified Workspace Baseline"]
    R["PASS report IDs"] --> B
    T["Task snapshot digest"] --> B
    W["Workspace digest"] --> B
    O["Output inventory"] --> B
    B --> N["다음 Task의 verification baseline"]
~~~

이 구분은 미커밋 변경을 안전하게 이어갈 때 중요합니다.

Task A가 파일을 변경하고 테스트를 통과하면 NotDone은 그 시점의 workspace digest와 파일 목록, PASS 보고서, Task snapshot을 묶습니다. Task B는 단순히 “A가 끝났다”는 문장이 아니라 이 baseline을 부모 상태로 받습니다.

작업 공간이 그 사이 달라지면 `workspace-diverged-from-verified-baseline`으로 중단합니다. 검증했던 바이트와 지금 실행할 바이트가 다르기 때문입니다.

## Retry는 다시 부탁하는 기능이 아닙니다

에이전트에게 “오류를 고쳐서 다시 해줘”라고 말하는 것은 retry policy가 아닙니다. 두 번째 시도에서 테스트를 삭제하거나 범위를 넓히면 성공처럼 보일 수 있습니다.

NotDone Retry Controller는 다음 불변 조건을 유지합니다.

- 실패한 request와 report를 보존
- attempt마다 새로운 run ID 사용
- acceptance criteria 유지
- verifier 강도 유지
- allowed path와 forbidden path 유지
- 동일한 verification baseline 유지
- Goal Plan과 CLI 제한 중 더 작은 시도 횟수 적용

~~~mermaid
flowchart TD
    F["실행 또는 검증 실패"] --> K{"retryOn에 포함?"}
    K -->|아니오| X["FAILED"]
    K -->|예| P{"금지 경로 변경?"}
    P -->|예| X
    P -->|아니오| S{"같은 실패 반복?"}
    S -->|예| H["BLOCKED: 사람 확인"]
    S -->|아니오| N["새 attempt 생성"]
    N --> V["같은 계약으로 전체 검증"]
~~~

현재 자동 retry 대상으로 분류되는 것은 `execution-failed`와 `verification-failed`입니다. 반복 실패, 금지 경로 변경, 모호한 상태, 횟수 소진은 사람에게 반환합니다.

## Human Evidence Queue

자동 테스트가 통과해도 실제 기기, 육안 검수, 외부 콘솔처럼 로컬 명령으로 확인할 수 없는 항목이 있습니다.

Provika를 예로 들면 다음과 같은 검증은 프로젝트 Goal Plan에 human evidence로 둘 수 있습니다.

- 실제 iPhone에서 촬영 결과 확인
- 지원 기기의 물리 Camera Control 동작 확인
- 백그라운드 또는 전화 interruption 확인
- 현지화 문구의 원어민 검수
- 스토어 availability 확인

NotDone은 자동 검증을 끝낸 뒤 이 항목만 queue에 모읍니다.

~~~text
notdone evidence pending

1 human evidence item(s) pending:
- device-review/gate.device-review.human:
  A person confirms the feature on a real device.
~~~

여기에는 의도적인 신뢰 경계가 있습니다. NotDone은 제출된 이미지나 동영상의 의미를 스스로 안다고 주장하지 않습니다.

1. 파일을 artifact store로 가져옵니다.
2. 파일과 evidence bundle의 digest를 계산합니다.
3. 사람이 내용을 검토합니다.
4. `--attest`를 사용해 바로 그 digest에 대한 PASS를 명시합니다.

따라서 **파일 import만으로는 PASS가 생기지 않습니다.** 사람이 확인했다는 행위가 digest에 결합돼야 합니다.

## Append-only artifact와 gate lock

각 실행은 나중에 다시 확인할 수 있도록 산출물을 남깁니다.

- immutable task snapshot
- Codex execution summary
- redacted terminal JSONL과 stderr
- exit record
- structured final output
- baseline diff
- automatic verifier evidence bundle
- VerificationReport

검증 보고서는 append-only ledger에 기록됩니다. gate가 PASS로 봉인된 뒤 기존 보고서를 조용히 수정하는 대신 새로운 사실은 새 기록으로 추가해야 합니다.

이 구조는 “최종 결과만 남기는 로그”와 다릅니다. 실패한 attempt도 왜 실패했는지 재구성할 수 있어야 다음 retry가 원래 계약을 약화하지 않았음을 확인할 수 있습니다.

## 런타임 중립 코어와 현재 실행 경계

NotDone의 계약, 증거, report, proof packet Schema는 특정 에이전트에 종속되지 않습니다. Claude Code, Codex, Gemini CLI에서 발생한 이벤트는 각 integration이 공통 형식으로 정규화합니다.

다만 v0.2.0 기준 `goal run`이 Task를 실제로 수행할 때 사용하는 공식 execution adapter는 `codex-exec`입니다.

| 영역 | v0.2.0 범위 |
|---|---|
| 공통 계약·증거·검증 | 런타임 중립 |
| Claude Code | plugin, MCP, 완료 gate |
| Codex | plugin, skill, MCP, 완료 gate, Goal execution |
| Gemini CLI | extension, MCP, 완료 gate |

완료 검증 연동과 자율 Goal 실행 지원을 같은 의미로 부르지 않는 이유입니다.

## 프로젝트 규칙은 코어 밖에 둡니다

Provika 사례가 NotDone의 요구사항을 드러내는 데 영향을 줬지만 다음 내용은 NotDone 코어에 들어가지 않습니다.

- Provika Task ID와 파일 경로
- Xcode 또는 Tuist 전용 명령
- 사진 Evidence v2의 제품 규격
- Camera Control 기기별 절차
- 현지화와 스토어 출시 정책

이 정보는 프로젝트가 소유하는 Goal Plan, verifier command, human evidence instructions에 있어야 합니다. NotDone은 그 규칙을 실행하고 증명하는 범용 계층입니다.

## 설계의 핵심

NotDone Goal Harness를 한 문장으로 요약하면 다음과 같습니다.

> 다음 프롬프트를 자동으로 만드는 시스템이 아니라, 다음 실행이 신뢰할 수 있는 상태에서 시작하도록 강제하는 시스템.

다음 글에서는 공식 npm 패키지를 설치하고 Goal Plan 검증, 실행, retry, 사람 증거 제출, resume까지 직접 사용하는 흐름을 살펴봅니다.

[3편: NotDone Goal Plan 실전 사용기]({% post_url 2026-08-02-notdone-goal-run-tutorial %})
