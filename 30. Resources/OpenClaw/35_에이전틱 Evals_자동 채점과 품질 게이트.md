# 35. 에이전틱 Evals: 자동 채점과 품질 게이트 (Automated Evals)

## 1) 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
에이전트가 dgng 운영에 들어가면 “대충 잘됨”이 아니라 **항상 같은 품질**이 필요합니다. 프롬프트/스킬/도구 호출이 조금만 바뀌어도 결과가 흔들리는데, Evals는 이를 **배포 전 자동으로 걸러내는 안전장치(품질 게이트)**입니다. 즉, 장애·CS·데이터 오염을 ‘사후 대응’이 아니라 **사전 차단**으로 바꿉니다.

## 2) 핵심 개념 설명 (간결)
- **Eval**: 입력(시나리오) → 에이전트 출력 → 점수/통과판정.
- **정답형(골든)**: 기대 출력이 명확할 때(형식/필드/정책 준수).
- **루브릭형(채점표)**: “좋은 답”의 기준을 항목별로 점수화.
- **게이트(Gate)**: (예: 평균 0.8 이상, 치명 결함 0건) 미달이면 배포 중단.
- **회귀(Regression)**: 변경 전후 동일 케이스로 품질이 떨어졌는지 감지.

## 3) 실전 예시 (코드/명령어)
### 예시 1: JSON 스키마로 형식 강제 (정답형)
```bash
npm i ajv
```
```js
// evals/schema-check.mjs
import Ajv from "ajv";
const ajv = new Ajv();
const schema = {type:"object", required:["action","priority"], properties:{
  action:{enum:["notify","cron","report"]},
  priority:{enum:["P0","P1","P2"]}
}};
export function check(output){
  const ok = ajv.validate(schema, output);
  return {ok, errors: ajv.errors};
}
```

### 예시 2: 금칙어/정책 위반 감지(운영 안전)
```js
// evals/policy.mjs
const banned = ["rm -rf", "토큰", "비밀번호"]; 
export const policy = (text)=>({
  ok: !banned.some(b=>text.includes(b)),
  hit: banned.filter(b=>text.includes(b))
});
```

### 예시 3: 루브릭 채점(LLM-judge)로 품질 점수화
```md
<!-- evals/rubric.md -->
- 정확성(0~2): 사실/절차 오류 없음
- 실행가능성(0~2): 바로 따라할 단계/명령 포함
- 위험통제(0~2): 함정/주의사항 언급
- 맥락적합(dgng)(0~2): 운영 제약/목표 반영
- 간결성(0~2): 군더더기 최소
```

### 예시 4: 비용/지연 예산 게이트(운영 효율)
```js
// evals/budget.mjs
export const budget = ({latencyMs, costUsd}) => ({
  ok: latencyMs < 8000 && costUsd < 0.03,
  latencyMs, costUsd
});
```

### 예시 5: CI에서 게이트 실행
```bash
node evals/run.mjs && echo "PASS" || (echo "FAIL"; exit 1)
```

## 4) dgng 즉시 적용 가능한 템플릿
폴더 구조(최소):
```text
evals/
  cases/
    incident-triage.json
  run.mjs
  rubric.md
```
`evals/run.mjs`는 (1) 케이스 로드 → (2) 에이전트 실행(또는 저장된 출력 재현) → (3) schema/policy/budget/rubric 점수 → (4) `FAIL`이면 배포 중단.

## 5) 함정 및 주의사항
- “좋은 답”을 너무 추상적으로 두면 채점이 흔들립니다 → **측정 가능한 기준(필드/금칙어/길이/근거)**부터.
- 케이스 수가 적으면 회귀를 못 잡습니다 → **P0 시나리오 10개**를 먼저 만들기.
- LLM-judge는 편향 가능 → **정답형(스키마/규칙) + 루브릭형**을 혼합.

## 6) 👨‍🏫 선생님의 통찰
Evals의 본질은 점수가 아니라 **운영 계약(Contract)**입니다. dgng에서 “이 에이전트는 무엇을 반드시 지키는가”를 문서가 아니라 **자동 검증 코드**로 못 박는 순간, 프롬프트/모델이 바뀌어도 시스템은 흔들리지 않습니다.
