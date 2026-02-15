# #15. OpenClaw Self-Update & Healthcheck: 시스템의 무결성과 최신성 유지

## 1. 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
프랭크, dgng와 같은 지식 기반 비즈니스를 자동화할 때 가장 위험한 것은 **'시스템의 노후화'**입니다. AI 기술은 매주 새로운 모델과 기능이 쏟아져 나옵니다. OpenClaw의 `gateway update.run`과 `healthcheck` 스킬은 단순한 유지보수 도구가 아니라, dgng 시스템이 항상 **최상의 성능(모델 연동)과 최고 수준의 보안(보안 패치)**을 유지하게 만드는 '생명 유지 장치'입니다.

## 2. 핵심 개념 설명
- **gateway update.run**: OpenClaw 코어와 종속성(dependencies)을 최신 상태로 업데이트하고 게이트웨이를 재시작합니다.
- **healthcheck (Skill)**: 호스트 머신의 보안 상태, 방화벽, SSH 설정 등을 점검하고 강화 제안을 제공합니다.
- **config.patch**: 전체 설정을 덮어쓰지 않고 특정 부분만 안전하게 수정하여 기능을 활성화합니다.

## 3. 실전 예시

### ① 최신 기능 및 모델 지원을 위한 즉시 업데이트
새로운 모델(예: Claude 4.5 등) 지원 패치가 나왔을 때 사용합니다.
```bash
# 게이트웨이 업데이트 실행 (재시작 포함)
openclaw gateway update.run
```

### ② 시스템 보안 정밀 진단
에이전트에게 시스템의 약점을 찾아내라고 명령합니다.
```javascript
// healthcheck 스킬 사용 예시 (에이전트 내부 호출)
nodes(action="run", command=["openclaw", "healthcheck", "status"]);
```

### ③ 업데이트 알림 자동화 (Cron 연동)
매주 월요일 아침, 업데이트 여부를 확인하고 보고받는 설정입니다.
```json
{
  "name": "Weekly Health Check",
  "schedule": { "kind": "cron", "expr": "0 9 * * 1" },
  "payload": { "kind": "agentTurn", "message": "시스템 헬스체크를 수행하고 업데이트가 필요한지 알려줘." },
  "sessionTarget": "isolated"
}
```

## 4. dgng 즉시 적용 가능한 템플릿
프랭크가 매달 한 번씩 시스템을 일괄 점검할 때 사용하는 체크리스트 에이전트 명령입니다.

> "선생님, dgng 시스템 정기 점검을 시작해줘. 1) healthcheck 스킬로 보안 상태 확인, 2) gateway update.run으로 최신 버전 확인, 3) MEMORY.md의 최근 1개월 기록 요약 및 정리. 결과를 보고해줘."

## 5. 함정 및 주의사항
- **업데이트 전 백업**: `update.run`은 강력하지만 드물게 설정 충돌을 일으킬 수 있습니다. 중요 파일(`.env`, `config.yaml`)은 항상 백업하세요.
- **재시작 지연**: 업데이트 후 게이트웨이가 재시작되는 동안 약 10~30초간 응답이 없을 수 있습니다.
- **권한 문제**: 특정 시스템 파일 수정 시 `sudo` 권한이나 elevated 모드가 필요할 수 있으니 에이전트의 권한 설정을 확인하세요.

## 6. 👨‍🏫 선생님의 통찰
프랭크, "도구는 닦을수록 빛난다"는 말이 있습니다. dgng의 뇌 역할을 하는 OpenClaw를 방치하지 마세요. 기술적 부채(Technical Debt)가 쌓이면 결국 결정적인 순간에 자동화가 멈추게 됩니다. 매일 아침 커피 한 잔과 함께 `📊 session_status`를 확인하고, 한 달에 한 번은 `healthcheck`를 통해 시스템의 근육을 점검하는 습관이 dgng의 지속 가능성을 결정할 것입니다. 

이것으로 **OpenClaw 기능 마스터 시리즈**의 모든 정규 과정을 마칩니다. 이제 프랭크는 단순한 사용자를 넘어 시스템 구축자(Architect)입니다. 축하합니다! 🎓🏆