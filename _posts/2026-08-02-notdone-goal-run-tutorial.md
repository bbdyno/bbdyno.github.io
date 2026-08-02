---
title: "NotDone Goal Plan 실전 사용기: 실행부터 사람 증거까지"
description: "notdone v0.2.0을 설치하고 Goal Plan 검증, Codex 실행, 제한된 재시도, Human Evidence 제출과 resume를 사용하는 방법"
author: bbdyno
date: 2026-08-02 19:00:00 +0900
categories: [Development, AI Agent]
tags: [NotDone, Goal Plan, Codex, npm, Tutorial, Human Evidence]
pin: false
math: false
mermaid: true
---

[1편]({% post_url 2026-08-02-notdone-development-story %})에서는 NotDone을 만든 이유를, [2편]({% post_url 2026-08-02-notdone-goal-harness %})에서는 Goal Harness의 내부 구조를 살펴봤습니다.

이번 글에서는 NotDone v0.2.0을 실제 프로젝트에 연결하는 흐름을 다룹니다.

목표는 다음 두 Task입니다.

1. 기능을 구현하고 자동 테스트를 통과합니다.
2. 빌드를 확인한 뒤 실제 기기 결과를 사람이 검수합니다.

자동 검증이 실패하면 고정된 조건으로 제한된 retry를 수행하고, 기기 검수 단계에서는 멈춰 필요한 증거만 요청하도록 구성합니다.

## 준비 사항

v0.2.0의 공식 Goal 실행 경로는 `codex-exec`입니다. 다음 환경이 필요합니다.

- Node.js 22 이상
- Git 저장소인 대상 프로젝트
- 프로젝트에서 실제로 실행 가능한 test와 build 명령
- 설치와 인증이 끝난 Codex CLI
- Goal Plan을 검토할 수 있는 사용자

NotDone은 Goal Plan에 적힌 명령을 실제로 실행합니다. 외부에서 받은 Plan은 셸 스크립트와 같은 수준으로 검토해야 합니다.

## 1. npm 패키지 설치

CLI와 MCP server는 독립 패키지입니다.

~~~shell
npm install --global notdone@0.2.0 notdone-mcp@0.2.0
notdone --version
~~~

`notdone`은 계약, Goal, 검증, proof 명령을 제공하고 `notdone-mcp`는 Claude Code, Codex, Gemini CLI 같은 MCP client에서 동일한 코어 기능을 사용할 때 필요합니다.

패키지는 다음 위치에서 확인할 수 있습니다.

- [notdone@0.2.0](https://www.npmjs.com/package/notdone/v/0.2.0)
- [notdone-mcp@0.2.0](https://www.npmjs.com/package/notdone-mcp/v/0.2.0)
- [GitHub Release v0.2.0](https://github.com/bbdyno/NotDone/releases/tag/v0.2.0)

## 2. 공식 Goal Plan 예제 가져오기

대상 프로젝트 루트에서 `.notdone` 디렉터리를 만들고 공식 예제를 가져옵니다.

~~~shell
mkdir -p .notdone
curl -fsSL \
  https://raw.githubusercontent.com/bbdyno/NotDone/v0.2.0/examples/goal-plan.json \
  -o .notdone/goal.json
~~~

공식 예제는 다음 흐름을 가지고 있습니다.

~~~mermaid
flowchart LR
    F["feature<br/>pnpm test"] --> D["device-review<br/>pnpm build"]
    D --> H["실제 기기 Human Evidence"]
~~~

예제의 `pnpm test`, `pnpm build`와 경로 범위는 자신의 프로젝트에 맞게 바꿔야 합니다.

예를 들어 TypeScript 프로젝트라면 다음처럼 설정할 수 있습니다.

~~~json
{
  "taskId": "feature",
  "title": "Implement the feature",
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
  "requiredPaths": ["src/**"],
  "forbiddenPaths": ["secrets/**"],
  "requiredVerifiers": [
    { "id": "gate.feature.automatic", "kind": "automatic" }
  ],
  "humanEvidenceRequirements": [],
  "retryPolicy": {
    "maxAttempts": 3,
    "retryOn": ["execution-failed", "verification-failed"]
  },
  "completionPolicy": {
    "requiredCriteria": "all",
    "humanEvidence": "required"
  }
}
~~~

iOS 프로젝트에서는 `pnpm test`를 실제 `xcodebuild test` 또는 프로젝트가 고정한 Tuist 명령으로 바꾸고, `allowedPaths`도 `Sources/**`와 `Tests/**`처럼 좁혀야 합니다.

여기서 핵심은 “에이전트가 알아서 테스트를 골라 실행하겠지”라고 두지 않는 것입니다. PASS를 만들 명령과 기대 종료 코드를 Goal Plan이 소유해야 합니다.

## 3. 실행 전에 Goal Plan 검증

~~~shell
notdone goal validate \
  --plan .notdone/goal.json \
  --json
~~~

정상이라면 Goal ID와 Task 수를 포함한 결과가 출력됩니다.

~~~json
{
  "valid": true,
  "goalId": "goal.example",
  "tasks": 2,
  "path": "/path/to/project/.notdone/goal.json"
}
~~~

이 단계에서는 JSON Schema, 필수 필드, ID, Task 의존성 같은 구조를 확인합니다. 테스트 명령이 제품 요구사항을 충분히 표현하는지는 여전히 Plan 작성자와 리뷰어의 책임입니다.

## 4. 사람 증거가 필요할 때까지 실행

대상 workspace와 artifact 위치를 명시해 실행합니다.

~~~shell
notdone goal run \
  --plan .notdone/goal.json \
  --target-workspace . \
  --artifact-root ../.my-project.notdone-artifacts \
  --until human-evidence \
  --max-retries 2 \
  --json
~~~

내부에서는 다음 작업이 순서대로 일어납니다.

1. 현재 Git revision과 workspace 상태를 baseline으로 캡처합니다.
2. 의존성이 충족된 `feature` Task를 선택합니다.
3. Task Template을 immutable request로 컴파일합니다.
4. Codex를 실행합니다.
5. 경로 범위와 `pnpm test` 결과를 검증합니다.
6. PASS하면 Verified Workspace Baseline을 저장합니다.
7. 그 baseline을 부모로 `device-review`를 실행합니다.
8. 자동 build가 PASS하면 사람 증거를 queue에 등록합니다.

사람 확인이 필요하면 결과의 `status`는 `HUMAN_EVIDENCE_REQUIRED`입니다.

~~~json
{
  "status": "HUMAN_EVIDENCE_REQUIRED",
  "goalId": "goal.example",
  "tasks": [
    { "taskId": "feature", "status": "VERIFIED", "attempts": 1 },
    {
      "taskId": "device-review",
      "status": "AWAITING_HUMAN_EVIDENCE",
      "attempts": 1
    }
  ],
  "pendingEvidence": [
    {
      "taskId": "device-review",
      "requirementId": "gate.device-review.human"
    }
  ]
}
~~~

위 출력은 이해를 돕기 위해 주요 필드만 표시한 것입니다. 이 상태는 오류가 아니라 의도한 중간 종료이며 CLI exit code는 `3`입니다. `set -e`를 사용하는 자동화에서는 이 코드를 예상 상태로 처리해야 합니다.

## 5. 실패와 retry 확인

첫 실행에서 test가 실패하면 해당 attempt는 지워지지 않습니다. NotDone은 실패 이유가 Goal Plan의 `retryOn`에 포함되고 금지 경로 변경이나 반복 실패가 없을 때만 새 attempt를 만듭니다.

~~~text
attempt 1
├─ frozen request
├─ terminal output
├─ exit record
└─ FAIL VerificationReport

attempt 2
├─ 새로운 run ID
├─ 동일한 acceptance criteria
├─ 동일한 path 제한
├─ attempt 1 실패 정보
└─ 전체 verifier 재실행
~~~

CLI의 `--max-retries 2`와 Task의 `maxAttempts`가 다르면 더 작은 범위가 적용됩니다. 예제의 feature Task는 최대 3 attempts이므로 최초 실행과 retry 두 번까지 가능합니다.

다음 경우에는 자동 retry 대신 중단합니다.

- 같은 이유의 실패가 반복됨
- `forbiddenPaths`에 해당하는 변경 발견
- 허용되지 않은 실패 종류
- workspace가 검증된 baseline에서 벗어남
- retry 횟수 소진

실패를 성공으로 바꾸기 위해 테스트를 제거하거나 verifier를 약하게 만드는 경로는 없습니다.

## 6. 대기 중인 사람 증거 확인

~~~shell
notdone evidence pending \
  --plan .notdone/goal.json \
  --target-workspace . \
  --artifact-root ../.my-project.notdone-artifacts \
  --json
~~~

각 항목에는 Task ID, requirement ID, 설명, 촬영 또는 검수 방법, 허용 media type이 들어 있습니다.

공식 예제의 요구사항은 다음과 같습니다.

~~~text
A person confirms the feature on a real device.

Capture the device result and confirm that the required behavior works.
허용 형식: image/png, video/mp4
~~~

실제 프로젝트에서는 “정상 동작 화면을 찍어 주세요”처럼 모호하게 쓰기보다 기기 모델, OS 버전, 조작 순서, 기대 결과를 Goal Plan에 고정하는 편이 좋습니다.

## 7. 증거 검토 후 attest

실제 기기에서 결과를 확인하고 workspace 밖에 증거 파일을 둡니다. workspace 안에 새 파일을 만들면 검증된 baseline과 달라질 수 있으므로 별도 디렉터리가 안전합니다.

~~~shell
mkdir -p ../notdone-evidence

notdone evidence submit \
  --plan .notdone/goal.json \
  --target-workspace . \
  --artifact-root ../.my-project.notdone-artifacts \
  --task-id device-review \
  --requirement-id gate.device-review.human \
  --file ../notdone-evidence/device-result.png \
  --captured-at "2026-08-02T18:30:00+09:00" \
  --media-type image/png \
  --attest \
  --json
~~~

`--attest`는 단순한 업로드 옵션이 아닙니다.

> 사용자가 이 파일의 내용을 검토했고, 해당 evidence bundle digest가 요구사항을 충족한다고 명시적으로 판정했다는 뜻입니다.

NotDone은 파일을 opaque evidence로 가져와 digest를 계산하고, 그 digest에 연결된 human PASS report를 append합니다. 이미지 의미를 모델이 임의로 해석해 PASS로 바꾸지 않습니다.

파일을 별도로 해시했다면 `--sha256`으로 예상 digest를 전달해 가져오는 파일이 바뀌지 않았는지도 확인할 수 있습니다.

## 8. Goal 재개

대기 중인 모든 사람 증거를 attest한 뒤 같은 Plan과 artifact root로 재개합니다.

~~~shell
notdone goal resume \
  --plan .notdone/goal.json \
  --target-workspace . \
  --artifact-root ../.my-project.notdone-artifacts \
  --until complete \
  --max-retries 2 \
  --json
~~~

human gate가 PASS로 봉인되고 모든 Task가 `VERIFIED`라면 최종 상태는 `COMPLETE`가 됩니다.

~~~json
{
  "status": "COMPLETE",
  "goalId": "goal.example",
  "tasks": [
    { "taskId": "feature", "status": "VERIFIED", "attempts": 1 },
    { "taskId": "device-review", "status": "VERIFIED", "attempts": 1 }
  ],
  "pendingEvidence": []
}
~~~

## 9. 남은 산출물 살펴보기

artifact root는 대상 Git workspace의 바깥에 두는 것이 기본 원칙입니다. 내부에는 Goal digest별 state, request, baseline과 run artifact가 남습니다.

~~~shell
find ../.my-project.notdone-artifacts \
  -maxdepth 5 \
  -type f \
  | sort
~~~

중요한 것은 최종 `COMPLETE` 문자열 하나가 아닙니다.

- 각 attempt에서 정확히 어떤 계약이 실행됐는가
- Codex가 어떤 종료 결과를 냈는가
- verifier가 어떤 명령과 파일을 확인했는가
- 실패한 attempt가 보존돼 있는가
- 다음 Task가 어떤 PASS baseline을 상속했는가
- 사람이 어떤 evidence digest에 attest했는가

이 질문에 답할 수 있어야 완료 결과를 나중에 재검토할 수 있습니다.

## 프로젝트에 적용할 때의 체크리스트

처음부터 큰 Goal 전체를 자동화하기보다 다음 순서로 적용하는 것을 권장합니다.

1. 반복 가능한 test와 build 명령부터 고정합니다.
2. 한 Task의 `allowedPaths`를 작게 설정합니다.
3. 의미 있는 `forbiddenPaths`를 정합니다.
4. 자동 check와 사람 check를 분리합니다.
5. retry 가능한 실패를 보수적으로 선택합니다.
6. 두 번째 Task를 추가해 successor baseline을 확인합니다.
7. 실패와 resume 시나리오를 일부러 한 번 실행합니다.

특히 “테스트가 통과한다”와 “제품이 올바르게 동작한다”를 같은 문장으로 두지 않는 것이 중요합니다. 전자는 command check로, 후자 중 자동화할 수 없는 부분은 구체적인 human evidence requirement로 나눠야 합니다.

## 마무리

NotDone의 Goal workflow는 에이전트에게 일을 더 많이 시키기 위한 기능이 아닙니다. 에이전트가 어떤 상태를 근거로 다음 일을 시작할 수 있는지 제한하기 위한 기능입니다.

~~~text
Goal Plan
→ 불변 Task 계약
→ 실행
→ 독립 검증
→ 제한된 retry 또는 사람 증거
→ 검증된 successor baseline
→ 다음 Task
~~~

이 흐름을 프로젝트에 맞게 작게 시작하면 “완료했다는 보고를 다시 확인하는 일”을 줄이고, 사람이 정말 판단해야 하는 단계에 집중할 수 있습니다.

전체 소스와 Schema, 예제는 [NotDone GitHub 저장소](https://github.com/bbdyno/NotDone)에서 확인할 수 있습니다.
