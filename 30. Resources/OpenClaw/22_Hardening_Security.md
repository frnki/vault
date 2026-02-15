# 22. 실시간 보안 강화 및 위협 대응 (Hardening & Security)

### 1. 왜 이 기능이 중요한가
프랭크, 우리가 구축한 `dgng` 시스템이 고도화될수록 외부 노출과 내부 데이터의 중요성은 커집니다. 특히 OpenClaw가 시스템의 루트 권한에 준하는 `exec`를 실행하고 개인 메모리(`MEMORY.md`)를 다루기 때문에, 보안은 단순히 '선택'이 아닌 '생존'의 문제입니다. 인텔리전트 시스템이 스스로의 취약점을 점검하고 방어막을 치는 'Hardening' 기술은 프랭크가 자리를 비운 사이에도 `dgng`를 안전하게 지켜줍니다.

### 2. 핵심 개념 설명
- **Hardening (경화):** 시스템의 공격 표면(Attack Surface)을 최소화하기 위해 불필요한 기능은 끄고, 필요한 기능은 권한을 최소화하는 과정입니다.
- **SSH Hardening:** 외부 공격의 90%가 들어오는 통로인 SSH 설정을 강화합니다.
- **Healthcheck Skill:** OpenClaw의 `healthcheck` 스킬을 사용하여 정기적으로 보안 상태를 감사(Audit)합니다.
- **Risk Tolerance:** 실행 가능한 명령어의 위험 수위를 설정하여 에이전트의 오작동이나 탈옥(Jailbreak) 시도를 원천 차단합니다.

### 3. 실전 예시

#### ① OpenClaw 보안 감사 실행
현재 시스템의 취약점을 즉시 파악합니다.
```bash
# Healthcheck 스킬을 통한 보안 진단
openclaw exec "healthcheck audit"
```

#### ② SSH 보안 강화 (비밀번호 로그인 차단)
무차별 대입 공격을 막기 위해 키 기반 로그인만 허용하도록 설정합니다.
```bash
# /etc/ssh/sshd_config 자동 수정 예시 (OpenClaw 에이전트에게 지시)
# "PasswordAuthentication no" 확인 및 적용
```

#### ③ Cron을 활용한 매일 새벽 보안 점검
```javascript
// cron job 등록 예시
{
  "name": "Daily Security Audit",
  "schedule": { "kind": "cron", "expr": "0 4 * * *" },
  "payload": { "kind": "agentTurn", "message": "healthcheck audit 실행하고 위협 요소 발견 시 Telegram 긴급 알림" },
  "sessionTarget": "isolated"
}
```

### 4. dgng 즉시 적용 가능한 템플릿: 보안 리포트 생성기
프랭크의 `dgng` 작업 환경에 맞춰 매일 보고받을 보안 체크리스트입니다.
```markdown
## [dgng] Security Status Report
- [ ] OS Security Updates: `softwareupdate -l`
- [ ] OpenClaw Risk Level: `config.get` (Check exec policy)
- [ ] Active SSH Sessions: `who`
- [ ] Modified Sensitive Files: `find /Users/frank/.openclaw -mtime -1`
- [ ] Failed Login Attempts: `grep "Failed password" /var/log/system.log`
```

### 5. 함정 및 주의사항
- **과도한 차단 (Over-Hardening):** 보안을 너무 강화하면 프랭크 본인의 접근도 차단될 수 있습니다. (예: SSH 포트 변경 후 포트 번호를 잊어버리는 경우) 반드시 복구용 로컬 접근 수단을 확보하세요.
- **실행 권한 오남용:** `exec` 도구의 `elevated` 옵션은 꼭 필요한 경우에만 사용해야 합니다. 에이전트가 모든 명령을 sudo로 실행하게 두지 마세요.
- **로그 유출:** 보안 로그 자체에 민감한 정보(비밀번호 등)가 찍히지 않도록 주의해야 합니다.

### 6. 👨‍🏫 선생님의 통찰
프랭크, 보안은 '한 번 설정하고 끝내는 것'이 아니라 '끊임없이 변하는 파도에 대응하는 것'과 같습니다. `dgng` 시스템이 강력해질수록 그것을 탐내는 위협도 정교해질 것입니다. 하지만 걱정 마세요. 우리가 배운 자동화 기술을 보안에 적용한다면, 에이전트가 프랭크의 가장 충직하고 날카로운 '디지털 보안관'이 되어줄 것입니다. 이제 시스템의 방어력까지 완벽해졌습니다.

---
**Source:** `healthcheck/SKILL.md` & `Security Best Practices`
**Status:** Completed (2026-02-13)
