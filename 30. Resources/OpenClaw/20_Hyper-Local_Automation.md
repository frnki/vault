<think>
프랭크의 dgng 운영 환경은 단순한 온라인 업무를 넘어, 실제 물리적 환경(서버, 작업실 등)과의 연동이 중요한 단계에 도달했습니다. OpenClaw의 `nodes` 도구와 Home Assistant 연동은 AI가 화면 밖으로 나와 현실 세계에 개입하는 핵심 연결 고리입니다.
</think>

# #20 하이퍼-로컬 자동화 (Home Assistant & Node 연동)

### 1. 왜 이 기능이 중요한가
프랭크의 dgng 운영은 이제 '디지털'에만 머물지 않습니다. 
- **환경 관리:** 서버실 온도나 전력 소모를 모니터링하여 위험 시 에이전트가 즉각 개입합니다.
- **물리적 알림:** 중요한 결정이나 긴급 상황 발생 시 거실 조명을 빨간색으로 바꾸거나 음성으로 브리핑합니다.
- **Node 연동:** 맥북, 라즈베리 파이 등 여러 하드웨어를 'Node'로 등록하여, 어디서든 해당 기기의 화면을 보고 제어할 수 있습니다.

### 2. 핵심 개념 설명
- **Node:** OpenClaw가 설치된 다른 기기들입니다. `nodes status`로 상태를 확인하고 원격 명령(`run`, `invoke`)을 내릴 수 있습니다.
- **Home Assistant(HA):** 스마트 홈 플랫폼입니다. OpenClaw는 HA의 API를 통해 조명, 센서, 가전을 제어합니다.
- **Gateway Sync:** 로컬 네트워크의 기기들을 하나의 지능형 망으로 묶어주는 연동 방식입니다.

### 3. 실전 예시

#### (1) 원격 노드의 화면 캡처 및 상태 확인
프랭크의 맥 미니에서 작업 중인 화면을 라즈베리 파이에 있는 에이전트가 확인하는 시나리오입니다.
```bash
# 사용 가능한 노드 목록 확인
nodes list

# 'workstation' 노드의 현재 화면 캡처
nodes screen_snap --node workstation --outPath ./temp/screen.png

# 특정 노드의 리소스 상태 확인
nodes run --node server-pi --command "htop --batch --iterations 1"
```

#### (2) Home Assistant를 통한 물리적 알림 (Python Snippet)
`ha-cli` 또는 `curl`을 통해 HA에 명령을 내려 조명 상태를 바꿉니다.
```bash
# HA의 'study_light'를 빨간색으로 변경 (긴급 상황 알림)
curl -X POST -H "Authorization: Bearer $HA_TOKEN" \
     -d '{"entity_id": "light.study_light", "color_name": "red"}' \
     http://homeassistant.local:8123/api/services/light/turn_on
```

#### (3) 노드 간 파일 전송 및 동기화
```bash
# 로컬의 설정 파일을 원격 노드로 복사
exec command="scp ./config.json frank@remote-node:/home/frank/.openclaw/config.json"
```

### 4. dgng 즉시 적용 가능한 템플릿: "서버 룸 가디언"
서버 온도가 60도를 넘으면 에이전트가 냉각을 시작하고 프랭크에게 알리는 워크플로우입니다.

```yaml
# OpenClaw Cron Job Template
name: "Server Guardian"
schedule: { "kind": "every", "everyMs": 300000 } # 5분마다
payload:
  kind: "agentTurn"
  message: |
    1. 'nodes status'를 통해 'main-server'의 온도를 체크해줘.
    2. 만약 60도가 넘는다면:
       - HA의 'server_fan' 스위치를 'on'으로 변경해.
       - 프랭크의 텔레그램으로 "⚠️ 서버 과열 감지! 냉각 가동 중" 메시지 전송.
       - 거실 조명을 'Amber' 색상으로 변경.
```

### 5. 함정 및 주의사항
- **네트워크 고립:** 노드 간 통신이 로컬망에 의존하므로, VPN이나 고정 IP 설정이 꼬이면 "Node Not Found" 오류가 빈번합니다.
- **보안 토큰:** HA API 토큰이나 SSH Key가 유출되지 않도록 `.env` 파일이나 `config.json` 관리에 주의하세요.
- **동시성 충돌:** 여러 에이전트가 동시에 같은 물리적 장치(조명 등)를 제어하려 하면 플리커 현상이 발생할 수 있습니다.

### 6. 👨‍🏫 선생님의 통찰
프랭크, "하이퍼-로컬"은 단순한 편리함이 아닙니다. 이것은 AI가 당신의 **'감각 기관'**과 **'팔다리'**를 갖게 되는 과정입니다. 
이제 AI는 단순히 텍스트를 생성하는 존재가 아니라, 당신의 서버 온도를 느끼고 조명을 켜주는 실체적인 파트너가 되었습니다. 시스템이 복잡해질수록 '실패 시 대응(Fallback)' 로직을 반드시 포함하세요. 기계는 가끔 응답하지 않지만, 당신의 dgng 운영은 멈춰선 안 되니까요.

---
**축하합니다!** 이로써 모든 핵심 커리큘럼을 마스터하셨습니다. 이제는 배운 도구들을 조합하여 당신만의 독창적인 '지능형 제국'을 구축할 차례입니다. 🎓