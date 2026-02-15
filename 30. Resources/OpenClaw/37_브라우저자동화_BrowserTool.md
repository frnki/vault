# 37. 브라우저 자동화 (browser tool): 대시보드/어드민을 “사람 대신 클릭”시키기

## 1) 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
서비스 운영에서 **가장 시간이 많이 새는 구간**은 “웹 어드민/대시보드 들어가서 확인→필요하면 조치→캡처/공유” 같은 **반복 클릭 업무**입니다. dgng 운영에서도 장애 징후 확인(그래프), 주문/정산 상태 점검, 공지/배너 반영, CS 티켓 조회 등은 결국 웹 UI로 모입니다. OpenClaw의 `browser`는 이 구간을 자동화해 **(1) 사람 실수 감소, (2) 정기 점검의 표준화, (3) 증거(스크린샷/PDF) 자동 저장, (4) 빠른 대응 루프**를 만듭니다.

## 2) 핵심 개념 설명 (간결)
- **profile**
  - `openclaw`: 격리된 브라우저(자동화 안정적)
  - `chrome`: 내 크롬 탭을 “릴레이”로 제어(확장프로그램에서 탭 연결 필요)
- **snapshot → act** 흐름
  - `snapshot`: 현재 화면을 구조적으로 읽음(버튼/입력칸 ref 확보)
  - `act`: ref를 클릭/입력/키누름 등으로 조작
- **refs="aria" 권장**: UI가 조금 바뀌어도 상대적으로 안정적으로 요소를 찾음

## 3) 실전 예시 4개 (명령/코드 포함)
> 아래는 “개념을 몸에 붙이는” 예시입니다. 실제 dgng URL/계정은 환경에 맞게 바꾸세요.

### 예시 1) 격리 브라우저로 로그인 페이지 열고 스냅샷
```bash
openclaw browser open --profile openclaw --targetUrl "https://dgng.example.com/admin/login"
openclaw browser snapshot --profile openclaw --refs aria --depth 6
```

### 예시 2) 아이디/비번 입력 후 로그인 버튼 클릭
(스냅샷 결과에서 `로그인`, `Email`, `Password`에 해당하는 ref를 확인 후 사용)
```bash
openclaw browser act --profile openclaw --request '{"kind":"type","ref":"e12","text":"ops@dgng.com"}'
openclaw browser act --profile openclaw --request '{"kind":"type","ref":"e15","text":"$DGNG_ADMIN_PASSWORD"}'
openclaw browser act --profile openclaw --request '{"kind":"click","ref":"e20"}'
```

### 예시 3) 지표 페이지로 이동 후 스크린샷 저장(운영 리포트 증거)
```bash
openclaw browser navigate --profile openclaw --targetUrl "https://dgng.example.com/admin/metrics"
openclaw browser screenshot --profile openclaw --fullPage true
```

### 예시 4) Chrome 릴레이로 ‘현재 내가 열어둔 탭’ 조작(수동+자동 혼합)
- 전제: 크롬에서 해당 탭을 열고 **OpenClaw Browser Relay 확장 아이콘을 눌러 “연결(배지 ON)”**
```bash
openclaw browser snapshot --profile chrome --refs aria
openclaw browser act --profile chrome --request '{"kind":"press","key":"Control+L"}'
openclaw browser act --profile chrome --request '{"kind":"type","text":"https://dgng.example.com/admin/orders"}'
openclaw browser act --profile chrome --request '{"kind":"press","key":"Enter"}'
```

## 4) dgng 즉시 적용 가능한 템플릿
**“오전 9시 운영 점검: 로그인→주요 KPI 캡처→주문 실패 건수 확인→스크린샷 첨부”**를 표준 동작으로 만듭니다.

- 체크리스트(최소):
  1) metrics(트래픽/에러율)
  2) orders(실패/환불 대기)
  3) payments(정산 지연)
  4) status page(외부 장애)

- 실행 골격(원리): `open → login → navigate → screenshot`를 한 흐름으로 고정
  - 비밀번호는 파일에 하드코딩하지 말고 **환경변수/시크릿 저장소**로

## 5) 함정 및 주의사항
- **2FA/캡차**: 완전 자동화가 막힐 수 있음 → 이때는 `chrome` 릴레이로 “사람이 2FA만 처리, 나머지 자동”이 현실적
- **ref 불안정**: UI 업데이트/AB테스트로 요소가 바뀌면 ref가 달라짐 → `refs="aria"` + 버튼 텍스트가 명확한 UI 설계가 중요
- **세션 만료**: 장시간 작업/크론 실행 시 재로그인이 필요 → 로그인 루틴을 템플릿화
- **민감정보 노출**: screenshot에 고객정보가 찍힐 수 있음 → 저장 경로/공유 채널을 운영정책으로 제한

## 6) 👨‍🏫 선생님의 통찰
브라우저 자동화는 “자동 클릭”이 아니라 **운영의 표준화**입니다. 사람이 매번 판단/기억에 의존하면 운영은 흔들립니다. 반대로, *매일 같은 순서로 같은 화면을 캡처하고 같은 기준으로 확인*하게 만들면, 이상 징후가 더 빨리 드러나고(차이가 보이니까) 조치도 빨라집니다. dgng에선 먼저 **‘매일 10분 쓰던 점검’을 자동화**해서 시간을 회수한 뒤, 그 시간을 **원인 분석/재발 방지(룰·알람·테스트)**에 재투자하는 게 가장 큰 레버리지입니다.
