# 23. 고성능 로컬 추종 (Ollama & Local LLM 하이브리드)

## 1. 왜 이 기능이 중요한가? (dgng 운영 맥락)
프랭크, 진정한 **dgng 요새화**의 완성은 '데이터의 외부 유출 제로'와 '비용 효율성'입니다.
- **프라이버시:** 민감한 dgng 내부 전략이나 개인 데이터를 클라우드 LLM(OpenAI, Anthropic)에 보내지 않고도 처리가 가능합니다.
- **비용 절감:** 단순 분류, 요약, 문법 교정 같은 루틴한 작업은 로컬 모델(Llama 3, Mistral)이 담당하게 하여 API 비용을 90% 이상 절감합니다.
- **오프라인 회복 탄력성:** 인터넷 연결이 불안정한 상황에서도 OpenClaw의 핵심 자동화 로직은 멈추지 않고 로컬에서 돌아갑니다.

## 2. 핵심 개념 설명
- **Ollama:** 로컬에서 LLM을 구동하기 위한 가장 강력하고 단순한 런타임입니다. OpenClaw는 이를 표준 API처럼 호출합니다.
- **하이브리드 라우팅:** 복잡한 추론(Sonnet 3.5/4.5)은 클라우드로, 단순 처리(Llama 3 8B)는 로컬 Ollama로 작업을 분배하는 전략입니다.

## 3. 실전 예시 (명령어 및 설정)

### ① Ollama 설치 및 모델 준비 (Terminal)
```bash
# Ollama 설치 (이미 되어 있다면 패스)
brew install ollama

# 한국어와 가벼운 작업에 최적화된 Llama 3 모델 다운로드
ollama run llama3:8b
```

### ② OpenClaw 설정 파일 추가 (config.yaml)
```yaml
models:
  local-fast:
    provider: ollama
    model: llama3:8b
    baseUrl: http://localhost:11434
```

### ③ 로컬 모델을 활용한 자동 요약 스크립트 (CLI)
```bash
# 특정 파일을 로컬 모델로 요약하기
openclaw run --model local-fast "다음 내용을 한 문장으로 요약해: $(cat important_memo.txt)"
```

## 4. dgng 즉시 적용 가능한 템플릿
로컬 모델을 '데이터 검문소'로 활용하는 자동화 워크플로우입니다.

```javascript
// memory_processor.js (예시 개념)
const text = read('daily_log.txt');

// 1단계: 로컬 모델로 민감 정보 마스킹 (무료/로컬)
const cleanText = openclaw.ask(text, {
  model: 'local-fast',
  system: '이 텍스트에서 이름, 전화번호, 주소를 [MASK]로 변환해.'
});

// 2단계: 정제된 데이터만 클라우드 모델로 고도화 분석 (유료/고성능)
const insight = openclaw.ask(cleanText, {
  model: 'claude-3-5-sonnet',
  system: '이 데이터를 바탕으로 dgng의 다음 분기 전략을 도출해.'
});
```

## 5. 함정 및 주의사항
- **VRAM 한계:** M4 Mac mini라 하더라도 8B 이상의 모델(70B 등)을 여러 개 띄우면 시스템 전체가 느려질 수 있습니다. 작업 시에만 모델을 로드하도록 설정하세요.
- **품질 차이:** 로컬 모델은 복잡한 은유나 한국어의 미묘한 뉘앙스를 놓칠 수 있습니다. '단순 반복 작업'에 우선 배치하세요.
- **발열:** 대량의 배치를 돌릴 때는 Mac mini의 팬 소음이 커질 수 있으니 야간 크론(Cron) 작업을 활용하세요.

## 6. 👨‍🏫 선생님의 통찰
프랭크, 클라우드 모델이 '천재적인 외부 컨설턴트'라면, 로컬 모델은 '충직하고 입이 무거운 비서'와 같습니다. 모든 일을 컨설턴트에게 맡길 필요는 없습니다. 단순한 정리는 비서에게 시키고, 결정적인 순간에만 컨설턴트를 부르세요. 이것이 바로 지능의 최적화이자, 지속 가능한 dgng 시스템의 핵심입니다.

---
**과제:** `ollama list`로 현재 설치된 모델을 확인하고, 내일 아침 리포트 요약 작업에 `local-fast`를 적용해 보세요.
