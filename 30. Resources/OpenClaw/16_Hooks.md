# OpenClaw 기능 마스터 시리즈: #16 이벤트 기반 자동화 (Hooks)

프랭크, 진정한 마스터 단계에 오신 것을 환영합니다. 지금까지 우리가 배운 기능들이 '내가 시킬 때 하는 일'이었다면, 오늘 다룰 **Hooks**는 '무슨 일이 생기면 알아서 하는 일'입니다. dgng 운영의 완전 무인화를 위한 마지막 퍼즐 조각입니다.

## 1. 왜 이 기능이 중요한가? (dgng 운영 맥락)
dgng는 단순한 봇이 아니라 프랭크의 사고를 확장하는 파트너입니다.
- **기록의 자동화:** `/new`로 대화를 초기화할 때, 이전 대화의 핵심을 자동으로 `MEMORY.md`에 요약 저장한다면?
- **감시 및 감사:** 프랭크가 자리를 비운 사이 누군가(또는 에이전트가) 내린 명령어를 로그로 남겨 추후 분석한다면?
- **워크플로우 연결:** 특정 명령어가 실행될 때 외부 API(예: 콘텐츠 발행)를 자동으로 트리거한다면?
Hooks는 이 모든 '사건'을 포착하여 즉각적으로 대응하게 해줍니다.

## 2. 핵심 개념 설명
- **Event-Driven:** 특정 사건(이벤트)이 발생하면 등록된 스크립트가 자동으로 실행되는 구조입니다.
- **주요 이벤트:**
    - `command:new`: `/new` 명령어로 세션을 초기화할 때
    - `command:reset`: `/reset`으로 세션을 리셋할 때
    - `gateway:startup`: OpenClaw 서버가 시작될 때
    - `agent:bootstrap`: 에이전트가 환경 설정을 로드하기 직전

## 3. 실전 예시 3-5개

### ① 세션 초기화 시 자동 요약 저장 (session-memory)
가장 추천하는 기본 훅입니다. 대화가 끝나고 `/new`를 칠 때 이전 대화 요약을 파일로 남깁니다.
```bash
openclaw hooks enable session-memory
```

### ② 명령어 실행 로그 기록 (command-logger)
모든 명령어를 JSONL 형식으로 기록합니다.
```bash
openclaw hooks enable command-logger
# 로그 확인: tail -f ~/.openclaw/logs/commands.log
```

### ③ 게이트웨이 시작 시 자동 실행 (boot-md)
서버가 켜지자마자 `BOOT.md`에 적힌 할 일을 수행합니다.
```bash
openclaw hooks enable boot-md
```

### ④ 사용자 정의 훅 (handler.ts 예시)
`/new` 명령 시 프랭크에게 환영 메시지를 보내는 간단한 훅입니다.
```typescript
import type { HookHandler } from "../../src/hooks/hooks.js";

const welcomeHook: HookHandler = async (event) => {
  if (event.action === "new") {
    event.messages.push("👋 프랭크, 새로운 탐구를 시작할 준비가 되었습니다!");
  }
};
export default welcomeHook;
```

## 4. dgng 즉시 적용 가능한 템플릿
dgng의 워크스페이스(`~/.openclaw/workspace-teacher/hooks/`)에 아래 구조로 폴더를 만드세요.

**`HOOK.md`**
```markdown
---
name: dgng-autosaver
description: "세션 종료 시 dgng 아카이브에 자동 저장"
metadata: { "openclaw": { "emoji": "📦", "events": ["command:new"] } }
---
# dgng 오토세이버
```

## 5. 함정 및 주의사항
- **성능 저하:** 훅은 명령 처리 과정에서 실행됩니다. 훅이 너무 무겁거나(예: 대용량 연산) 외부 API 응답을 무한정 기다리면 응답 속도가 느려집니다. (비동기 처리 권장)
- **무한 루프:** 훅 내부에서 다시 명령어를 발생시키는 로직이 있으면 무한 루프에 빠질 수 있으니 주의하세요.
- **활성화 후 재시작:** 훅을 `enable`한 뒤에는 반드시 `openclaw gateway restart`를 해야 적용됩니다.

## 👨‍🏫 선생님의 통찰
프랭크, Hooks는 OpenClaw를 '도구'에서 '시스템'으로 진화시키는 핵심입니다. 특히 `session-memory` 훅은 필수입니다. 우리가 나눈 대화가 휘발되지 않고 자산으로 쌓이는 첫 걸음이기 때문이죠. 이제 dgng는 당신이 잠든 사이에도 스스로를 기록하고 관리하는 유기적인 시스템이 될 준비가 되었습니다.

---
**수료 완료:** #16 이벤트 기반 자동화 (Hooks)
**다음 과제:** (모든 필수 커리큘럼이 완료되었습니다. 이제 자유로운 실험의 시간입니다!)
