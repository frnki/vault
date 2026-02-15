# 39. 노드+Cron 런북: 현장 점검/복구를 정시 자동화

## 1) 왜 중요한가(dgng)
현장 장애는 피하기 어렵고, 비용은 **탐지 지연**과 **복구 편차**에서 발생합니다. `nodes`로 원격 조치하고 `cron`으로 정시에 런북을 돌리면 체크/복구가 표준화되어 MTTR이 줄고, 실패해도 기록이 남습니다.

## 2) 핵심 개념
- 런북: **체크 → 조치 → 재검증 → 보고**
- `cron`은 트리거, 실행은 `sessionTarget=isolated`의 `agentTurn`
- 원칙: **정상이면 아무 것도 하지 않기(멱등)**

## 3) 실전 예시(3개)

### A) 매일 09:00 런북 스케줄
```json
{"name":"dgng:runbook:daily","schedule":{"kind":"cron","expr":"0 9 * * *","tz":"Asia/Seoul"},"sessionTarget":"isolated","payload":{"kind":"agentTurn","message":"dgng-node-1: /health→실패면 restart→30초 후 재검증→요약"}}
```

### B) 헬스체크 실패 시에만 재시작
```js
const r=await nodes.run({node:"dgng-node-1",command:["bash","-lc","curl -fsS 127.0.0.1:8080/health"],commandTimeoutMs:15000})
if(!String(r).includes('ok')) await nodes.run({node:"dgng-node-1",command:["bash","-lc","sudo systemctl restart dgng"],commandTimeoutMs:60000})
```

### C) 결과만 알림
```js
await nodes.notify({node:"dgng-node-1",title:"dgng 런북",body:"FAIL→restart→OK"})
```

## 4) dgng 템플릿
```text
대상:<node>/<svc>
check: curl /health (15s)
fix: restart 1회
recheck: 30s 후
report: OK|RESTARTED|FAILED
```

## 5) 함정/주의
- `tz` 미지정=UTC 착각 → Asia/Seoul
- timeout/권한/오프라인 대비: 실패도 보고
- 재시작 폭주 금지: 1회 실행당 1회 제한

## 6) 👨‍🏫 통찰
자동화의 핵심은 **대응을 같은 절차로 만드는 것**입니다. v1을 만들고 장애마다 1줄씩 개선하면 런북이 운영 자산이 됩니다.
