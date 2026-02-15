- 날짜: 2026-02-15 08:09 (KST)
- 링크: https://summarize.sh/
- 출처 제목(title): summarize

## 한 줄 요약
- **summarize.sh**는 링크/문서/미디어를 자동 추출해 CLI와 Chrome 사이드패널에서 바로 요약해주는 도구.

## 핵심 포인트
- URL, PDF, 이미지, 오디오/비디오, YouTube, 팟캐스트까지 폭넓게 처리.
- **추출 파이프라인**(Readability/markitdown + Firecrawl fallback)으로 본문 정제 품질 확보.
- 미디어는 **트랜스크립트 우선**, 필요 시 Whisper fallback.
- 출력은 Markdown/JSON/metrics 지원으로 자동화 파이프라인 구성에 유리.
- Chrome Side Panel + 로컬 데몬(localhost, token-protected)으로 브라우징 중 즉시 요약 가능.

## 생각할 거리(질문)
- 클리핑 워크플로우에서 CLI 일괄 처리와 브라우저 즉시 요약 중 어디가 더 ROI가 큰가?
- Obsidian 저장 템플릿을 “원문 링크 + 5줄 요약 + 다음 액션”으로 고정하면 재활용성이 더 높아질까?
- DGNG/브랜딩/투자/건강/OpenClaw 축으로 태그 체계를 먼저 고정할 필요가 있을까?

## 프랭크에게 중요한 이유
- 링크 소비를 **지식 자산화**(요약 + 구조화 + 저장)로 전환 가능.
- Obsidian 누적 시 리서치 회수 속도와 재사용성이 크게 올라감.
- OpenClaw 운영 관점에서 자동 요약 파이프라인 표준 도구로 적합.

## 다음 액션
- `summarize "https://..." --json` 결과를 Obsidian Inbox에 자동 적재하는 스크립트 작성.
- 기본 태그 세트 확정: #dgng #branding #investment #health #openclaw

## 실행 메모
- summarize CLI로 실제 확인 완료 (`summarize "https://summarize.sh/" --length medium`).

## 태그
#clipping #ai-tools #workflow #automation #summarize
