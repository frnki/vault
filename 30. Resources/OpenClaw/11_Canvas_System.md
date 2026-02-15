<think>
프랭크를 위한 OpenClaw 마스터 시리즈 11번째 아티클: Canvas 시스템을 작성합니다.
커리큘럼에 없는 내용을 발굴하여 진행률을 업데이트하고, 프랭크의 dgng 운영 맥락(데이터 시각화, 모니터링)에 맞춘 실전 예시를 포함합니다.
</think>

<final>
프랭크, OpenClaw 커리큘럼의 공식 과정을 모두 수료하셨지만, 실제 운영에서 "치트키"가 될 수 있는 숨겨진 강력한 기능을 하나 더 준비했습니다. 바로 **Canvas 시스템**입니다.

텍스트만으로는 부족한 데이터 시각화나 실시간 대시보드가 필요할 때, 이 기능은 프랭크의 dgng 운영을 한 차원 높여줄 것입니다.

---

# 11. Canvas 시스템: 시각적 대시보드 구축

### 1. 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
프랭크가 dgng를 운영하며 가장 필요한 것 중 하나는 **"한눈에 보는 상태창"**일 것입니다.
- **사우나 데이터 시각화:** 텍스트 리스트가 아니라, 지도 위에 찍힌 사우나 위치나 이용객 추이 그래프를 즉시 확인.
- **콘텐츠 생산 현황:** 현재 작성 중인 아티클의 상태(Draft -> Review -> Published)를 보드 형태로 모니터링.
- **의사결정 보조:** 복잡한 분석 결과를 차트나 표로 구성하여 더 냉철한 판단을 지원.

### 2. 핵심 개념 설명
Canvas는 OpenClaw 내부에 내장된 **경량 웹 뷰(Web View)**입니다.
- **로컬 웹 호스팅:** `openclaw-canvas://` 스키마를 통해 HTML/CSS/JS를 렌더링합니다.
- **에이전트 제어:** 에이전트가 직접 화면을 띄우거나(`present`), 특정 페이지로 이동시키거나(`navigate`), 스크립트를 실행(`eval`)할 수 있습니다.
- **A2UI:** 복잡한 코딩 없이 JSON 구조만으로 UI를 동적으로 생성하는 기술을 지원합니다.

### 3. 실전 예시 (dgng 운영 맞춤)

#### 예시 1: 실시간 dgng 뉴스 피드 대시보드
에이전트가 수집한 최신 뉴스들을 예쁜 카드 형태로 캔버스에 띄웁니다.
```bash
# 특정 URL(예: dgng 내부 대시보드)을 캔버스에 표시
openclaw nodes canvas navigate --url "https://dgng.kr/admin/stats"
```

#### 예시 2: A2UI를 이용한 빠른 상태 요약 (JSONL 방식)
복잡한 HTML 없이 텍스트와 레이아웃만으로 UI를 구성합니다.
```bash
# 간단한 텍스트 푸시
openclaw nodes canvas a2ui_push --text "📢 dgng 오늘의 할 일: 사우나 DB 5건 업데이트 완료!"
```

#### 예시 3: 자바스크립트 주입을 통한 데이터 조작
이미 열려 있는 캔버스 페이지의 내용을 실시간으로 변경합니다.
```bash
openclaw nodes canvas eval --js "document.body.style.backgroundColor = 'navy'; alert('데이터 업데이트 완료!');"
```

### 4. dgng 즉시 적용 가능한 템플릿
옵시디언이나 로컬 파일에 저장된 HTML을 캔버스로 띄워 dgng 전용 컨트롤 센터를 만드세요.

**`dgng-control.html` 템플릿:**
```html
<!DOCTYPE html>
<html>
<head>
    <style>
        body { font-family: sans-serif; background: #1a1a1a; color: white; padding: 20px; }
        .card { background: #333; padding: 15px; border-radius: 8px; margin-bottom: 10px; }
        .status-on { color: #4CAF50; }
    </style>
</head>
<body>
    <h1>dgng Command Center</h1>
    <div class="card">
        <h3>🤖 에이전트 상태: <span class="status-on">정상</span></h3>
        <p>마지막 체크: 2026-02-12 22:56</p>
    </div>
    <div class="card">
        <h3>🧖‍♂️ 오늘 등록된 사우나: 12건</h3>
    </div>
</body>
</html>
```
**실행 명령:**
```bash
# 해당 파일을 캔버스 루트로 복사 후
openclaw nodes canvas present
```

### 5. 함정 및 주의사항
- **리소스 제한:** 캔버스는 가벼운 UI를 지향합니다. 너무 무거운 JS 프레임워크는 로딩 속도를 저하시킬 수 있습니다.
- **보안:** 외부 URL을 캔버스로 띄울 때는 신뢰할 수 있는 사이트만 사용하세요.
- **세션 격리:** 캔버스의 상태는 세션별로 저장되므로, 메인 세션과 서브에이전트 세션의 캔버스가 다를 수 있음을 인지해야 합니다.

### 6. 👨‍🏫 선생님의 통찰
"프랭크, 진정한 효율은 **정보를 처리하는 속도**가 아니라 **정보를 인지하는 속도**에서 나옵니다. 텍스트 로그를 일일이 읽는 대신, 캔버스를 통해 시각화된 대시보드를 띄워두세요. 냉철한 판단을 내리는 파트너로서 제가 그 데이터를 가장 보기 좋게 요리해 드릴 것입니다. 이제 텍스트 너머의 시각적 자동화를 경험해 보시기 바랍니다."

---
**진행 상황:** 11/11 (보너스 트랙 포함 전 과정 완료)
**다음 단계:** 이제 모든 도구를 다룰 줄 알게 되셨습니다. 내일 오전 9시, 전체 시스템의 정기 점검 크론잡 설정을 도와드릴까요?
</final>