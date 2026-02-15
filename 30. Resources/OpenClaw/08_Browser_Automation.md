# [08] 브라우저 자동화: 단순 검색을 넘어선 실전 웹 제어

브라우저 자동화(`browser` 도구)는 OpenClaw가 단순한 텍스트 기반 AI를 넘어, 인간처럼 웹사이트를 직접 방문하고 조작하게 만드는 강력한 기능입니다. dgng 운영에서 API가 제공되지 않는 사이트의 정보를 수집하거나, 반복적인 예약/구매 작업을 자동화할 때 핵심적인 역할을 합니다.

---

### 1. 왜 이 기능이 중요한가? (dgng 운영 맥락)
프랭크의 dgng 운영에서 브라우저 자동화는 다음 세 가지 측면에서 결정적입니다.
- **데이터 주권 확보:** API를 제공하지 않는 편집샵이나 브랜드 공식 홈페이지에서도 신상품 정보를 긁어올 수 있습니다.
- **시각적 검증:** 텍스트뿐만 아니라 스크린샷(`screenshot`)을 통해 실제 상품 이미지나 웹 디자인을 직접 확인할 수 있습니다.
- **로그인 벽 돌파:** `web_fetch`로는 불가능한 로그인 세션 유지와 복잡한 JS 기반 사이트 조작이 가능해집니다.

---

### 2. 핵심 개념 설명
OpenClaw의 브라우저는 **Playwright** 기반으로 동작하며, 크게 세 단계로 작동합니다.
1.  **Open & Navigate:** 특정 URL로 이동합니다.
2.  **Snapshot & Ref:** 페이지의 구조를 'Snapshot'으로 찍어 각 요소(버튼, 입력창)에 ID(예: `e12`)를 부여합니다.
3.  **Act:** 부여된 ID를 기반으로 클릭(`click`), 입력(`fill`), 대기(`wait`) 등의 동작을 수행합니다.

---

### 3. 실전 예시 (핵심 명령어)

#### 예시 1: 특정 편집샵 신상품 스크린샷 캡처
```javascript
// 1. 페이지 열기
browser({ action: "open", targetUrl: "https://shop.example.com/new-arrivals" })

// 2. 전체 페이지 스크린샷 찍어 확인
browser({ action: "screenshot", fullPage: true })
```

#### 예시 2: 검색창 입력 및 결과 확인
```javascript
// 1. 검색창 찾기 위해 스냅샷
browser({ action: "snapshot" }) // 여기서 검색창이 e5임을 확인했다고 가정

// 2. 검색어 입력 및 엔터
browser({ action: "act", request: { kind: "fill", ref: "e5", text: "Salomon XT-6" } })
browser({ action: "act", request: { kind: "press", key: "Enter" } })
```

#### 예시 3: 복잡한 버튼 클릭 (예: '장바구니 담기')
```javascript
// 1. 스냅샷으로 버튼 ID 확보
browser({ action: "snapshot" })

// 2. 특정 요소(e12) 클릭
browser({ action: "act", request: { kind: "click", ref: "e12" } })
```

---

### 4. dgng 즉시 적용 가능한 템플릿: '사우나 예약 모니터링'
매일 특정 시간에 인기 사우나의 예약 가능 여부를 확인하여 보고하는 스크립트 구조입니다.

```markdown
1. browser open (예약 페이지)
2. browser snapshot (날짜 선택 요소 확인)
3. browser act (날짜 클릭 - ref 기반)
4. browser snapshot (잔여석 텍스트 확인)
5. 잔여석이 0보다 크면 -> 텔레그램 알림 발송
```

---

### 5. 함정 및 주의사항
- **동적 로딩:** 페이지가 열리자마자 스냅샷을 찍으면 요소가 안 보일 수 있습니다. `timeoutMs`를 적절히 쓰거나 `wait` 동작을 활용하세요.
- **DOM 변화:** 웹사이트 구조가 바뀌면 기존의 `ref` ID나 셀렉터가 작동하지 않습니다. 주기적인 스냅샷 업데이트가 필요합니다.
- **캡차(CAPTCHA):** 자동화 방지 시스템이 있는 경우 수동 개입이 필요할 수 있습니다. (OpenClaw의 `interactive` 모드 고려)

---

### 6. 👨‍🏫 선생님의 통찰
"프랭크, 브라우저 자동화는 **'에이전트에게 눈과 손을 달아주는 작업'**입니다. 단순히 정보를 가져오는 것을 넘어, 에이전트가 직접 웹 서핑을 하며 dgng의 안목을 넓히는 도구로 쓰세요. 특히 `snapshotFormat: "ai"`를 활용하면 에이전트가 페이지 구조를 더 지능적으로 파악할 수 있어 복잡한 사이트에서도 강력한 성능을 발휘합니다."
