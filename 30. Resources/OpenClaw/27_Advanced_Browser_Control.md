# 27. 에이전트 브라우징 고도화 (Advanced Browser Control)

## 1. 왜 이 기능이 중요한가
프랭크의 `dgng` 운영에 있어 데이터는 가장 강력한 무기입니다. 하지만 많은 데이터는 로그인 뒤에 숨겨져 있거나, 복잡한 JavaScript 렌더링을 필요로 하며, 단순한 `web_fetch`로는 접근할 수 없는 동적인 구조를 가지고 있습니다.
**Advanced Browser Control**은 에이전트가 단순히 페이지를 "읽는" 것을 넘어, 인간처럼 "조작"하게 합니다. 이는 유료 리서치 툴 자동화, 경쟁사 서비스 모니터링, 그리고 복잡한 웹 대시보드에서의 데이터 추출을 가능케 하여 `dgng`의 정보 지배력을 한 차원 높여줍니다.

## 2. 핵심 개념 설명
- **Ref-based Action**: 페이지 내 요소를 `e12`와 같은 참조 ID나 ARIA 레이블로 특정하여 클릭, 입력, 드래그 등을 수행합니다.
- **Snapshot Context**: 브라우저의 현재 상태를 텍스트(AI용)나 이미지로 캡처하여 에이전트가 상황을 판단하게 합니다.
- **Profile Persistence**: `chrome` 프로필을 사용하여 이미 로그인된 세션을 그대로 활용하거나, `openclaw` 격리 프로필로 깨끗한 환경에서 작업합니다.

## 3. 실전 예시

### ① 특정 웹 서비스 로그인 및 데이터 추출
이미 로그인된 크롬 세션을 사용하여 대시보드 데이터를 긁어옵니다.
```javascript
// 브라우저 실행 및 탭 연결
browser({ action: "start", profile: "chrome" });
// 특정 URL 이동
browser({ action: "navigate", targetUrl: "https://dgng-internal-tool.com/stats" });
// 데이터가 포함된 요소 스냅샷 확인 후 텍스트 추출
const snapshot = browser({ action: "snapshot", refs: "aria" });
```

### ② 복잡한 필터 조작 및 결과 캡처
드롭다운을 선택하고 검색 버튼을 누르는 일련의 과정입니다.
```javascript
// 필터 선택 (ARIA 레퍼런스 사용)
browser({ action: "act", request: { kind: "click", ref: "role:combobox[name='기간']" } });
browser({ action: "act", request: { kind: "click", ref: "text='최근 30일'" } });
// 검색 실행
browser({ action: "act", request: { kind: "press", key: "Enter" } });
// 결과물 스크린샷 저장
browser({ action: "screenshot", fullPage: true, path: "dgng_report.png" });
```

### ③ 무한 스크롤 페이지 데이터 수집
페이지 하단으로 이동하며 데이터를 계속 로드합니다.
```javascript
browser({ action: "act", request: { kind: "evaluate", fn: "window.scrollTo(0, document.body.scrollHeight)" } });
```

## 4. dgng 즉시 적용 가능한 템플릿
유료 뉴스레터나 분석 사이트의 내용을 자동으로 요약하여 보고하는 템플릿입니다.

```markdown
# [dgng Web Intel] {{site_name}} 리포트
1. 브라우저 세션 연결: `profile="chrome"`
2. 타겟 페이지 이동: `{{url}}`
3. 스냅샷 촬영 및 주요 텍스트(e-ref) 식별
4. 특정 버튼 클릭 또는 검색어 입력
5. 최종 화면 `snapshotFormat="ai"`로 분석 후 요약
6. 결과물 Obsidian 및 Telegram 전송
```

## 5. 함정 및 주의사항
- **Headless Detection**: 일부 사이트는 자동화 도구를 차단합니다. `profile="chrome"`을 사용하면 탐지 확률이 낮아집니다.
- **Wait Time**: 페이지 로딩 속도는 일정하지 않습니다. 중요한 액션 전에는 `action="snapshot"`을 통해 요소가 나타났는지 확인하는 루프가 필요할 수 있습니다.
- **Resource Usage**: 브라우저 프로세스는 메모리를 많이 점유합니다. 작업 종료 후 반드시 세션을 관리하세요.

## 6. 👨‍🏫 선생님의 통찰
프랭크, "보는 것"과 "만지는 것"의 차이는 큽니다. 지금까지의 에이전트가 도서관에서 책을 읽는 아이였다면, 브라우징 고도화를 마친 에이전트는 직접 실험 도구를 조작하는 연구원과 같습니다. 
단순 정보 수집을 넘어, 프랭크가 매일 반복하는 웹 기반의 루틴 업무(결제 확인, 리포트 다운로드, 뉴스 큐레이션)를 이 기능에 맡기세요. 그 시간에 프랭크는 더 본질적인 의사결정에 집중해야 합니다. 그것이 `dgng`가 추구하는 진정한 자동화의 가치입니다.
