- 날짜: 2026-02-15 14:37 (KST)
- 링크: https://developer.chrome.com/blog/webmcp-epp?hl=ko
- 출처 제목(title): WebMCP를 사전 체험판으로 이용할 수 있습니다. | Blog | Chrome for Developers

## 한 줄 요약
- Google이 웹사이트가 AI 에이전트와 더 정확하고 안정적으로 상호작용하도록, WebMCP라는 구조화된 도구 노출 표준(선언형/명령형 API)을 사전 체험판으로 공개.

## 핵심 포인트
- 핵심 문제의식: DOM 클릭 위주의 에이전트 동작은 모호하고 취약해, 사이트가 **명시적 액션 인터페이스**를 제공해야 함.
- 제안 API 2종: **선언적 API**(HTML 폼 기반 표준 작업) + **명령형 API**(JS 필요한 동적 작업).
- 기대효과: 사용자 대신 수행하는 예약/쇼핑/지원 업무에서 속도·정확도·재현성 개선.
- 대표 시나리오: 고객지원 티켓 자동 입력, 이커머스 옵션 탐색/결제 흐름, 여행 검색·필터·예약.
- 현재 상태: Early Preview Program(EPP) 통해 문서/데모 접근 및 실험 가능.

## 실행 메모
- 일반 링크는 `markdown.new/<원본URL>` 우선 워크플로로 요약 진행.
- 원문 사실관계(게시일/핵심 문구)는 developer.chrome.com 본문으로 교차 확인.

## summarize.sh raw output
```text
WebMCP is a Chrome for Developers initiative aimed at making websites agent ready by standardizing how sites expose structured tools that AI agents can use to take actions for the user more quickly, reliably, and precisely. The post argues that explicitly defining tools tells agents how and where to interact with a site, reducing ambiguity versus raw DOM clicking and enabling faster, more robust workflows for tasks like booking, support, or navigating complex data.

It proposes two browser APIs to support these structured interactions: a Declarative API for standard actions definable in HTML forms, and an Imperative API for more complex, dynamic flows requiring JavaScript execution. The article highlights practical use cases in customer support, ecommerce, and travel, and invites developers to join an early preview program to access documentation and demos and track ongoing changes.

*WebMCP aims to provide a standard way for exposing structured tools, ensuring AI agents can perform actions on your side with increased speed, reliability, and precision.*

*These APIs serve as a bridge, making your website agent-ready and enabling more reliable and performant agent workflows compared to raw DOM actuation.*
via model cli/codex/gpt-5.2

8.9s · cli/codex/gpt-5.2 · ↑15k ↓224 Δ15k
```

## 태그
#clipping #webmcp #chrome #agent-web #mcp #ai
