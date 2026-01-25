---
title: " Vercel, 지난 10년 동안 React / Next.js 최적화하며 쌓은 노하우를 집대성하고 공개한.."
source: "https://x.com/lucas_flatwhite/status/2011797805334192474"
author:
  - "[[@lucas_flatwhite]]"
published: 2026-01-15
created: 2026-01-16
description:
tags:
  - "clippings"
---
**lucas** @lucas\_flatwhite 2026-01-15

Vercel, 지난 10년 동안 React / Next.js 최적화하며 쌓은 노하우를 집대성하고 공개한..

\> React Best Practices 프로젝트

📑 Introducing: React Best Practices

https://vercel.com/blog/introducing-react-best-practices…

📦 GitHub - React Best Practices

https://github.com/vercel-labs/agent-skills/tree/main/skills/react-best-practices…

이건 단순한 가이드라인을 넘어 Cursor, Claude Code 같은 AI 에이전트가 코드를 분석할 때 바로 참고할 수 있도록 설계된 일종의.. 최적화된 사전이라고 생각하시면 됩니다.

기존의 성능 최적화는 보통 문제가 터진 후에 대응하는 방식이었습니다.

하지만 Vercel은 이를 체계화하여 Impact,, 우선순위에 따라 40개 이상의 규칙을 정리했습니다. 사람이 읽기 좋을 뿐만 아니라, LLM이 이해하기 쉬운 구조로 만들어져 있어서 최곱니다.

↓

이 가이드는 성능 최적화를 8개 영역으로 나눕니다.

1\. 비동기 폭포수 제거

2\. 번들 크기 최적화

3\. 서버 사이드 성능

4\. 클라이언트 데이터 페칭

5\. 리렌더링 최적화

6\. 렌더링 성능

7\. 고급 패턴

8\. JavaScript 성능

↓

이 프로젝트의 진가는 http://AGENTS.md 파일에 있습니다.

Vercel은 이 규칙들을 'Agent Skills'로 패키징했습니다.

\> npx add-skill vercel-labs/agent-skills

이 명령어를 통해 Cursor, Claude Code, OpenCode 같은 AI 도구에 이 규칙들을 이식할 수 있습니다.

> 2026-01-15
> 
> We just released 𝚛𝚎𝚊𝚌𝚝-𝚋𝚎𝚜𝚝-𝚙𝚛𝚊𝚌𝚝𝚒𝚌𝚎𝚜, a repo for coding agents.
> 
> React performance rules and evals to catch regressions, like accidental waterfalls and growing client bundles.
> 
> How we collected them and how to install the skill ↓
> 
> ![이미지](https://pbs.twimg.com/media/G-tXNPabQAU4wBV?format=jpg&name=large) ![이미지](https://pbs.twimg.com/media/G-tXNJ6bsAAHS2b?format=jpg&name=large) ![이미지](https://pbs.twimg.com/media/G-tXNJ8bQAA3iZP?format=jpg&name=large)