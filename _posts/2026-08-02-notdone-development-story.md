---
title: "에이전트의 '완료'를 믿지 않기: NotDone 개발기"
description: "Provika의 실제 개발 흐름에서 출발해 AI 코딩 에이전트의 완료를 증거로 검증하는 오픈소스 NotDone을 만들기까지"
author: bbdyno
date: 2026-08-02 11:00:00 +0900
categories: [Development, AI Agent]
tags: [NotDone, AI Agent, Agent Harness, Verification, Codex, Open Source]
pin: true
math: false
mermaid: true
---

AI 코딩 에이전트와 작업하다 보면 자주 만나는 문장이 있습니다.

> 구현을 완료했고 모든 테스트가 통과했습니다.

문장은 확신에 차 있지만, 그 문장 자체로는 아무것도 증명하지 못합니다. 실제로 어떤 명령을 실행했는지, 종료 코드는 무엇이었는지, 허용하지 않은 파일까지 바꾸지는 않았는지, 테스트 이후 작업 공간이 다시 달라지지는 않았는지 알 수 없기 때문입니다.

[NotDone](https://github.com/bbdyno/NotDone)은 이 간극을 메우기 위해 만든 오픈소스입니다.

> 에이전트는 “완료”라고 말합니다. NotDone은 증거를 요구합니다.

이 글에서는 NotDone이 어떤 문제에서 시작했고, 실제 앱 개발에 사용하던 하네스가 어떻게 범용 완료 증명 계층으로 발전했는지 정리합니다.

## 출발점은 기능 구현이 아니라 증거였습니다

Provika에서는 촬영 결과를 단순 이미지 파일이 아닌 검증 가능한 증거 패키지로 다루는 작업을 진행하고 있었습니다. Git 이력을 시간순으로 보면 작업의 성격이 명확합니다.

1. Tuist 기반 프로젝트의 이식성을 복구했습니다.
2. 기존 Evidence Core의 동작을 characterization test로 고정했습니다.
3. 메타데이터를 읽을 때 성공과 실패 상태를 명시했습니다.
4. 사이드카 공개키로 서명을 독립 검증하도록 만들었습니다.
5. 오프라인에서도 증거 패키지를 검증할 수 있게 했습니다.
6. 사진 증거 v2 포맷과 fixture를 테스트로 고정했습니다.

여기서 중요한 점은 “파일을 하나 만들었다”가 완료 조건이 아니라는 사실입니다. 기존 포맷과의 호환성, 서명 검증, 패키지 무결성, 실제 촬영 환경은 서로 다른 종류의 확인을 요구합니다.

~~~mermaid
flowchart LR
    A["기존 동작 고정"] --> B["새 포맷 구현"]
    B --> C["자동 테스트"]
    C --> D["오프라인 독립 검증"]
    D --> E["실제 기기 확인"]
    E --> F["출시 판단"]
~~~

앞의 네 단계는 명령과 파일로 자동 검증할 수 있지만, 실제 카메라 동작이나 물리 버튼처럼 사람이 기기에서 확인해야 하는 단계도 있습니다. 하나의 “테스트 통과” 문장으로 이 모두를 덮어버리면 무엇이 검증됐고 무엇이 남았는지 알 수 없습니다.

## 프롬프트로 만든 하네스의 한계

처음에는 작업마다 다음 내용을 프롬프트에 넣는 방법을 사용했습니다.

- 이번 Task에서 바꿀 수 있는 경로
- 반드시 만족해야 하는 acceptance criteria
- 실행해야 할 테스트와 빌드 명령
- 실패했을 때 허용할 재시도
- 다음 Task가 이어받을 기준 상태
- 실제 기기에서 사람이 제출해야 할 증거

이 방식도 단일 Task에서는 꽤 잘 동작합니다. 하지만 작업이 길어지면 문제가 생겼습니다.

Markdown 작업 목록에는 다음 Task가 `READY`라고 적혀 있는데 실행 가능한 계약 파일은 없었습니다. 첫 Task가 실패하면 기존 조건을 유지한 retry 요청을 사람이 다시 작성해야 했고, PASS한 미커밋 변경을 다음 Task가 어떤 기준으로 신뢰해야 하는지도 매번 설명해야 했습니다.

결국 하네스를 아는 사람만 하네스를 계속 움직일 수 있었습니다.

~~~text
작업 설명
→ 사람이 계약 작성
→ 에이전트 실행
→ 사람이 결과 해석
→ 사람이 retry 계약 작성
→ 사람이 successor 기준 작성
→ 다음 작업
~~~

자동화의 문제가 아니라 **실행 가능한 상태 모델이 없었던 것**입니다.

## 완료 문장과 완료 증거를 분리하다

NotDone의 첫 번째 원칙은 단순합니다.

> 에이전트가 작성한 완료 문장은 검증 증거로 사용하지 않는다.

대신 작업 전에 계약을 고정하고, 계약에 연결된 검증을 NotDone이 직접 실행합니다.

~~~mermaid
flowchart TD
    U["사용자 목표"] --> C["고정된 계약"]
    C --> A["에이전트 실행"]
    A --> E["도구에서 증거 수집"]
    E --> V["독립 검증"]
    V -->|PASS| P["Proof packet과 보고서"]
    V -->|FAIL| R["실패 근거 보존"]
    V -->|사람 필요| H["Human Evidence Queue"]
~~~

계약에는 “로그인 오류를 수정한다” 같은 자연어만 들어가지 않습니다. 그 주장을 어떻게 확인할지도 함께 들어갑니다.

~~~yaml
claims:
  - id: regression-test
    statement: 로그인 회귀 테스트가 통과한다
    required: true
    checks:
      - type: command
        command: npm test -- login-crash
        expect:
          exitCode: 0
~~~

NotDone은 명령, 종료 코드, 파일, Git diff, 로그, 외부 증거를 수집하고 필수 주장을 `verified`, `unverified`, `blocked`, `failed`로 판정합니다.

## 단일 Task 실행기에서 목표 하네스로

2026년 7월 25일 공개한 v0.1.0은 검증 가능한 단일 Task 실행에 집중했습니다.

- 런타임 중립 JSON Schema
- canonical JSON과 SHA-256 무결성
- command, file, Git diff 검증
- proof packet
- `notdone` CLI와 `notdone-mcp`
- Claude Code, Codex, Gemini CLI 완료 게이트

실제 프로젝트에 적용하면서 다음 질문이 남았습니다.

> 첫 Task가 PASS한 뒤, 누가 다음 계약을 만들고 실행할 것인가?

이 질문이 v0.2.0의 출발점이 됐습니다. 8월 1일에는 workspace preflight, append-only artifact store, 터미널 산출물, baseline diff, 외부 증거 lifecycle, verification report ledger와 gate lock이 차례로 추가됐습니다. 그 위에 실행 하네스와 self-test를 연결했습니다.

8월 2일에는 다음 기능을 공식 배포판에 포함했습니다.

- 기계가 읽는 Goal Plan
- READY Task를 계약으로 바꾸는 JIT Task Compiler
- 상태를 실제 보고서와 맞추는 State Reconciler
- 검증 조건을 완화하지 않는 제한된 Retry Controller
- 검증된 미커밋 결과를 넘기는 Verified Workspace Baseline
- 사람만 할 수 있는 검증을 모으는 Human Evidence Queue

초기 개발 맥락을 기록한 두 커밋 제목에는 `Provika harness workflow`라는 표현이 남아 있습니다. 하지만 커밋의 파일 내용과 현재 NotDone 소스, npm 패키지에는 Provika 전용 코드나 문자열이 없습니다. 프로젝트별 Task ID, Xcode 명령, 촬영 규칙은 NotDone 코어가 아니라 외부 Goal Plan과 프로젝트 정책에 남기는 것이 설계 원칙입니다.

## 왜 Git 커밋만으로는 부족했나

연속된 에이전트 작업에서 매 Task마다 커밋을 만들게 할 수도 있습니다. 하지만 검증 전에 커밋을 허용하면 실패한 결과가 이력에 들어가고, 커밋을 금지하면 다음 Task가 이전 변경을 어떤 근거로 이어받을지 애매해집니다.

NotDone은 `Verified Workspace Baseline`을 사용합니다.

~~~text
Verified Workspace Baseline
= Git base revision
+ 이전 PASS 보고서 ID
+ 변경할 수 없는 Task snapshot digest
+ 검증된 workspace digest
+ output file inventory
~~~

Git HEAD가 같아도 작업 공간의 내용이 다르면 다른 상태입니다. 반대로 아직 커밋하지 않은 변경이라도 정확한 파일 목록과 digest, 그 상태를 PASS시킨 보고서가 결합돼 있다면 다음 Task의 검증 기준으로 사용할 수 있습니다.

이 기능 덕분에 다음 흐름이 가능합니다.

~~~text
Task A 실행
→ 자동 검증 PASS
→ successor baseline 저장
→ 커밋 없이 Task B 실행
→ Task B는 A의 검증된 결과만 상속
~~~

## 자동화하지 말아야 할 것도 자동으로 판단하기

목표 하네스라고 해서 모든 판단을 모델에 맡기지는 않습니다.

컴파일 오류나 테스트 assertion 실패처럼 계약 범위 안에서 고칠 수 있는 문제는 새 attempt로 재시도할 수 있습니다. 이때 기존 request와 실패 보고서를 보존하고, acceptance criteria와 verifier, allowed path를 그대로 유지합니다.

반면 다음 상황에서는 사람에게 제어권을 돌려줍니다.

- 같은 실패가 반복됨
- forbidden path 변경이 필요함
- 계약 자체가 모호함
- 실제 기기 조작이 필요함
- 법률·번역·스토어 상태처럼 외부 판단이 필요함

NotDone의 목표는 사람을 없애는 것이 아닙니다. **자동으로 검증 가능한 작업은 끝까지 진행하고, 사람이 필요한 판단만 정확한 근거와 함께 모아 보여주는 것**입니다.

## NotDone이 보장하지 않는 것

NotDone이 PASS를 만들었다고 해서 코드가 세상의 모든 상황에서 올바르다는 뜻은 아닙니다. 계약에 표현되지 않은 의미적 정확성은 검증할 수 없습니다.

현재 신뢰 모델은 정직하지만 실수할 수 있는 로컬 에이전트를 대상으로 합니다. 같은 사용자 또는 root 권한으로 검증기와 저장소를 모두 변조하는 악성 프로세스, 손상된 컴파일러나 운영체제까지 방어한다고 주장하지 않습니다.

또한 코어 프로토콜과 검증은 런타임 중립적이지만, v0.2.0의 `goal run` 실행 어댑터는 현재 `codex-exec`입니다. Claude Code와 Gemini CLI에는 각 런타임의 완료 검증 연동이 있지만, Goal 실행 경로까지 모두 동일하다고 표현하지 않습니다.

## 이제 “완료”는 상태가 아니라 검증 결과입니다

NotDone을 만들면서 가장 크게 바뀐 것은 명령의 개수가 아니었습니다. 완료를 바라보는 기준이 바뀌었습니다.

~~~text
이전: 에이전트가 완료했다고 보고했다
이후: 고정된 계약의 모든 필수 gate가 근거와 함께 PASS했다
~~~

NotDone v0.2.0은 [GitHub Release](https://github.com/bbdyno/NotDone/releases/tag/v0.2.0)와 [npm의 notdone 패키지](https://www.npmjs.com/package/notdone/v/0.2.0), [notdone-mcp 패키지](https://www.npmjs.com/package/notdone-mcp/v/0.2.0)로 공개돼 있습니다.

다음 글에서는 Goal Plan이 Task 계약, retry, successor baseline, 사람 증거로 이어지는 내부 구조를 살펴봅니다.

[2편: 검증된 상태를 다음 작업으로 넘기는 법]({% post_url 2026-08-02-notdone-goal-harness %})
