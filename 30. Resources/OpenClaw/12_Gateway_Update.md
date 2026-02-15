# 12. Gateway 설정 및 셀프 업데이트 (update.run & config.patch)

### 1. 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
OpenClaw는 단순한 봇이 아니라, 프랭크의 **dgng 비즈니스 인프라**입니다. 인프라에서 가장 중요한 것은 '가동률'과 '최신성'입니다.
- **안정성:** 수동으로 설정을 건드리다 시스템이 꼬이는 일을 방지합니다.
- **자기 진화:** 프랭크가 일일이 업데이트 명령을 내리지 않아도, 에이전트가 스스로 최신 버전을 유지하며 새로운 기능을 습득합니다.
- **원격 제어:** 터미널 접속 없이 텔레그램 대화만으로 시스템의 핵심 설정을 안전하게 변경할 수 있습니다.

---

### 2. 핵심 개념 설명
- **gateway config.get/patch:** 현재 설정을 불러오고, 특정 부분만 안전하게 수정(Merge)합니다. 전체 파일을 덮어쓰지 않아 위험이 적습니다.
- **gateway update.run:** 소스 코드와 의존성을 최신으로 업데이트합니다.
- **Auto-Restart:** `patch`나 `update` 직후 시스템은 자동으로 재시작되며, 마지막 세션으로 돌아와 작업 완료를 보고합니다.

---

### 3. 실전 예시

#### 예시 1: 시스템 최신 상태 유지 (업데이트)
```javascript
// 시스템 업데이트 실행
await gateway({ action: "update.run", reason: "정기 보안 및 기능 업데이트" });
```

#### 예시 2: 특정 에이전트의 모델 변경 (부분 수정)
```javascript
// 특정 에이전트의 기본 모델을 즉시 변경
await gateway({ 
  action: "config.patch", 
  raw: JSON.stringify({
    agents: {
      "researcher": { model: "google-antigravity/claude-sonnet-4-5" }
    }
  })
});
```

#### 예시 3: 텔레그램 알림 설정 수정
```javascript
await gateway({
  action: "config.patch",
  raw: JSON.stringify({
    channels: {
      telegram: { minimal: true } // 알림 최소화 모드 활성화
    }
  })
});
```

---

### 4. dgng 즉시 적용 가능한 템플릿
**[매주 월요일 새벽 자동 업데이트 크론잡]**
```json
{
  "name": "Weekly System Maintenance",
  "schedule": { "kind": "cron", "expr": "0 4 * * 1" },
  "payload": {
    "kind": "agentTurn",
    "message": "Gateway update.run을 실행해서 시스템을 최신 상태로 유지해줘. 완료 후 리포트를 남겨줘."
  },
  "sessionTarget": "isolated"
}
```

---

### 5. 함정 및 주의사항
1. **의존성 충돌:** `update.run` 도중 커스텀하게 수정한 코드가 있다면 충돌이 발생할 수 있습니다. 중요한 변경사항은 항상 `skills/` 디렉토리에 별도로 관리하세요.
2. **재시작 대기:** 명령 후 약 10~30초간 응답이 없을 수 있습니다. 이는 시스템이 재부팅되는 과정이므로 중복 명령을 보내지 마세요.
3. **config.apply vs patch:** `apply`는 전체 설정을 대체합니다. 실수하면 시스템 접속이 끊길 수 있으니, 웬만하면 `patch`를 사용하세요.

---

### 6. 👨‍🏫 선생님의 통찰
"프랭크, 진정한 자동화는 에이전트가 '관리의 대상'을 넘어 '관리의 주체'가 될 때 완성됩니다. Gateway 도구는 에이전트에게 자신의 몸체(설정)를 고치고 업그레이드할 수 있는 열쇠를 주는 것과 같습니다. 이 기능을 마스터함으로써 프랭크의 dgng 시스템은 24시간 멈추지 않고 스스로 진화하는 진정한 AI 인프라로 거듭날 것입니다."
