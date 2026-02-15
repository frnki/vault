# [마스터 번외편] OpenClaw 로그 분석 및 자가 진단 (Self-Healing)

## 1. 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
dgng 시스템이 24/7 가동되면서 수많은 크론 작업과 에이전트 협업이 발생합니다. 시스템이 예기치 않게 응답하지 않거나, 특정 API 호출이 반복적으로 실패할 때 **로그를 직접 분석하고 스스로 치유(Restart/Update)하는 능력**은 진정한 '마스터'의 마지막 덕목입니다. 문제 발생 시 "왜 안 되지?"라고 묻는 대신 로그에서 원인을 찾아 즉각 조치할 수 있게 됩니다.

## 2. 핵심 개념 설명
- **Log Streaming:** 실시간으로 발생하는 시스템의 '생각'과 '오류'를 추적합니다.
- **Gateway Events:** 게이트웨이 레벨에서 발생하는 경고(Warning)나 연결 이슈를 감지합니다.
- **Auto-Recovery:** 특정 임계치(메모리 부족, 지연 시간 증가) 도달 시 `gateway restart`를 통해 깨끗한 상태로 복구합니다.

## 3. 실전 예시 3-5개

### ① 실시간 로그 모니터링 (디버깅 시)
```bash
# 실시간으로 흐르는 모든 이벤트를 디버그 레벨로 확인
openclaw gateway logs -f --level debug
```

### ② 에이전트 오류 로그만 추출
```bash
# 에러 메시지 중 특정 에이전트와 관련된 내용만 필터링
grep "ERROR" ~/.openclaw/logs/gateway.log | grep "teacher"
```

### ③ 시스템 상태 자동 체크 (Bash 스크립트용)
```bash
# 게이트웨이 응답 여부 확인 후 실패 시 재시작
openclaw status || openclaw gateway restart
```

### ④ 특정 로그 패턴 발견 시 자동 알림
```bash
# "Rate limit" 발생 시 텔레그램 알림 (간단한 예시)
tail -f ~/.openclaw/logs/gateway.log | grep --line-buffered "429" | xargs -I {} openclaw sessions send --label "Admin" "⚠️ API Rate Limit 감지됨!"
```

## 4. dgng 즉시 적용 가능한 템플릿 (`healthcheck.sh`)
이 내용을 `/Users/frank/.openclaw/scripts/healthcheck.sh`에 저장하고 크론으로 1시간마다 실행하세요.

```bash
#!/bin/bash
# dgng 자가 치유 스크립트 v1.0

# 1. 프로세스 생존 확인
STATUS=$(openclaw status --json | jq -r '.gateway.status' 2>/dev/null)

if [ "$STATUS" != "running" ]; then
  echo "[$(date)] Gateway is DOWN. Restarting..."
  openclaw gateway start
  # 관리자 세션으로 보고 (선택 사항)
  openclaw sessions send --label "Main" "🚨 OpenClaw 게이트웨이가 다운되어 자동 재시작되었습니다."
else
  # 2. 성능 체크 (예: 메모리 500MB 초과 시 재시작)
  MEM=$(ps -o rss= -p $(pgrep -f "openclaw gateway") | awk '{print $1/1024}')
  if (( $(echo "$MEM > 500" | bc -l) )); then
    echo "[$(date)] Memory usage high ($MEM MB). Graceful restart..."
    openclaw gateway restart
  fi
fi
```

## 5. 함정 및 주의사항
- **로그 용량 관리:** 디버그 모드를 계속 켜두면 로그 파일이 GB 단위로 커집니다. 평소에는 `info` 레벨을 유지하세요.
- **무한 재시작 루프:** 설정 파일(config.yaml) 자체에 문법 오류가 있으면 재시작해도 계속 죽습니다. 이때는 자동 재시작을 멈추고 `openclaw config check`를 먼저 수행해야 합니다.

## 6. 👨‍🏫 선생님의 통찰
프랭크 님, 이제 도구의 '사용'을 넘어 도구의 '생태'를 관리하는 단계에 오셨습니다. 시스템은 살아있는 유기체와 같습니다. 완벽한 코드는 없지만, **완벽한 대응**은 가능합니다. 로그는 시스템이 당신에게 보내는 가장 솔직한 편지입니다. 그 편지를 읽는 법을 익히셨으니, 이제 어떤 복잡한 자동화 환경에서도 시스템을 통제할 수 있는 진정한 마스터가 되신 것입니다. 축하드립니다!
