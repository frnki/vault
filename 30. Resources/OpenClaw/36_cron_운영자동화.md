# 36_cron_운영자동화

## 1) 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
서비스(dgng)를 운영하다 보면 “사람이 기억해야 하는 일”이 장애의 씨앗이 됩니다. 배포 후 30분 모니터링, 매일 아침 로그/에러율 확인, 인증서 만료 점검, 주간 리포트 작성 같은 반복 업무는 **정확한 시간**에 **빠짐없이** 실행되어야 합니다. OpenClaw의 `cron`은 이를 **에이전트 실행(분석/요약/조치)** 또는 **시스템 이벤트(리마인더)** 로 자동화해, 운영 품질을 사람의 컨디션과 기억력에서 분리합니다.

## 2) 핵심 개념 설명 (간결)
- **cron job = (언제) schedule + (무엇을) payload + (어디에) sessionTarget**
- `sessionTarget=isolated` + `payload.kind=agentTurn`: 정해진 시간에 **에이전트를 돌려 결과를 자동 공지**(운영 점검/리포트에 최적)
- `sessionTarget=main` + `payload.kind=systemEvent`: 메인 세션에 **리마인더 텍스트를 주입**(“지금 뭐 할 시간”)
- `schedule.kind`: `cron`(정시/요일), `every`(간격), `at`(1회)

## 3) 실전 예시 (3–5개)
### 예시 1) 매일 09:10 운영 체크리스트 생성(격리 세션)
```json
{
  "name": "dgng-daily-ops",
  "schedule": { "kind": "cron", "expr": "10 9 * * *", "tz": "Asia/Seoul" },
  "payload": {
    "kind": "agentTurn",
    "message": "dgng 오늘 운영점검: 에러율/지연/배포변경/알람 요약하고 조치 우선순위 3개 제안해줘",
    "timeoutSeconds": 180
  },
  "sessionTarget": "isolated",
  "enabled": true
}
```

### 예시 2) 배포 후 30분, 2시간 “리마인더”(1회)
```json
{ "schedule": {"kind":"at","at":"2026-02-13T14:00:00+09:00"},
  "payload": {"kind":"systemEvent","text":"[리마인더] dgng 배포 후 30분: 에러/레이트리밋/핵심 플로우 확인"},
  "sessionTarget":"main" }
```

### 예시 3) 15분마다 장애 징후 감시(간격)
```json
{ "schedule": {"kind":"every","everyMs":900000},
  "payload": {"kind":"agentTurn","message":"최근 15분 dgng 알람/로그 요약하고 이상 징후 있으면 한 줄 결론+다음 액션"},
  "sessionTarget":"isolated" }
```

### 예시 4) 수동 즉시 실행(새 job 검증)
- 새로 만든 job은 **바로 한 번** 돌려 결과 형식/권한/시간을 검증하세요.

## 4) dgng 즉시 적용 가능한 템플릿
아래 프롬프트만 바꿔서 복제하세요.
```json
{
  "name": "dgng-<purpose>",
  "schedule": { "kind": "cron", "expr": "<m h * * dow>", "tz": "Asia/Seoul" },
  "payload": {
    "kind": "agentTurn",
    "message": "[dgng 운영] (1) 지표 요약 (2) 리스크 TOP3 (3) 바로 할 조치 3개 (4) 필요하면 질문 1개",
    "timeoutSeconds": 180
  },
  "sessionTarget": "isolated",
  "enabled": true
}
```

## 5) 함정 및 주의사항
- **시간대(tz) 누락**: 서버/환경 기본값이 UTC면 9시가 18시에 울립니다. 항상 `Asia/Seoul` 명시.
- **main vs isolated 혼동**: 운영 리포트는 `isolated(agentTurn)`가 안전(컨텍스트 오염/토큰 폭주 방지).
- **너무 촘촘한 주기**: 1–5분 단위는 비용/노이즈가 급증. “15분/1시간”부터 시작.
- **리마인더 문구 빈약**: 실행 시점에 의미가 살아야 합니다. “무엇을/어디를/끝내면 무엇이 좋은지”를 적기.

## 6) 👨‍🏫 선생님의 통찰
cron은 ‘자동화’가 아니라 **운영 루틴의 제품화**입니다. dgng에서 반복 작업을 cron으로 고정하면, 사람은 “기억” 대신 “판단”에 집중하게 됩니다. 처음엔 과감하게 적게(하루 1개) 시작하고, 결과가 좋았던 루틴만 늘리세요. 자동화는 많을수록 좋은 게 아니라, **살아남은 루틴만 남기는 것**이 진짜 실력입니다.
