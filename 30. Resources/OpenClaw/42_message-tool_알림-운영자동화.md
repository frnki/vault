# 42. 메시지 운영 자동화: `message` 툴로 알림/상태/버튼까지

## 1) 왜 중요한가 (dgng 운영 맥락)
운영의 비용은 ‘수리 시간’보다 **인지 지연**에서 폭발합니다. 지표 이상·장애·배포 결과가 로그에만 있으면 의사결정이 늦고, 늦으면 피해가 커집니다. `message`는 cron/nodes/browser가 만든 결과를 **텔레그램 운영방으로 “결정 가능한 형태”로 배송**하는 마지막 1m입니다.

## 2) 핵심 개념
- `send`: 메시지/미디어 전송(최종 보고)
- `edit`: 같은 메시지를 갱신(도배 방지, 상태판)
- `poll`: 즉시 의사결정(롤백/유지)
- `buttons`: 사람 결재(승인 기반 자동화)

## 3) 실전 예시 4개
### (1) 장애/이상 알림
```json
{"action":"send","channel":"telegram","target":"<ops-room>","message":"[dgng 알림] API p95 800ms↑(5m). 영향: 로그인 느림. 다음: DB 커넥션/최근배포 확인"}
```

### (2) 데일리/정시 점검 요약
```json
{"action":"send","channel":"telegram","target":"<ops-room>","message":"[dgng 점검 03:00]\n- API p95 220ms OK\n- DB OK (conn 41/200)\n- Queue lag 0\n- 조치: 없음"}
```

### (3) 승인 버튼(배포/복구 안전장치)
```json
{"action":"send","channel":"telegram","target":"<ops-room>","message":"[dgng 배포] v1.42.4 체크 완료. 진행?","buttons":[[{"text":"진행","callback_data":"deploy:go"},{"text":"중단","callback_data":"deploy:stop"}]]}
```

### (4) 상태판처럼 “수정”으로 갱신
```json
{"action":"edit","channel":"telegram","target":"<ops-room>","messageId":"12345","message":"[dgng 상태] 03:05 DB OK / API DEGRADED(p95 650ms) / Queue OK"}
```

## 4) dgng 즉시 적용 템플릿
**장애 알림(5줄 룰)**
```
[dgng 알림] <증상>
- 영향: <사용자/기능>
- 범위: <서비스/리전/버전>
- 근거: <지표 수치/링크>
- 다음: <1~2개 액션>
```

## 5) 함정/주의
- **알림 폭탄:** 같은 이상 반복 → 쿨다운(예: 30분) 또는 `edit` 기본
- **타겟 실수:** 개인 DM/그룹/채널 ID 다름 → 작은 테스트 방에서 먼저 검증
- **장문 금지:** 운영자는 10초 안에 읽어야 함(핵심+링크)
- **무승인 자동조치:** 위험한 복구/배포는 버튼 승인(사람 결재) 붙이기

## 6) 👨‍🏫 선생님의 통찰
좋은 알림은 “불안”이 아니라 **결정**을 전달합니다. dgng에선 `message`를 출력 포맷터로 두고, *영향·범위·근거·다음 액션*을 한 화면에 고정하세요. 그게 자동화의 신뢰를 만듭니다.
