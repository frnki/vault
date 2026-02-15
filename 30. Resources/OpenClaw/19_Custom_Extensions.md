# #19 OpenClaw 커스텀 확장 (Custom Extensions)

## 1. 왜 이 기능이 중요한가 (프랭크의 dgng 운영 맥락)
프랭크, 지금까지 우리는 OpenClaw가 제공하는 기본 도구들(web_search, browser, memory 등)을 아주 잘 활용해 왔습니다. 하지만 **dgng(Decision-making Guide & Next Generation)** 시스템이 고도화될수록, OpenClaw의 기본 기능만으로는 부족한 순간이 옵니다.

예를 들어, 프랭크만의 독자적인 내부 데이터베이스에 직접 쿼리를 날리거나, 특정 로컬 스크립트를 즉시 실행하여 결과를 분석 보고서에 포함해야 할 때가 있습니다. **커스텀 확장(Custom Extensions)**은 OpenClaw를 단순한 '비서'에서 프랭크의 시스템에 완벽히 통합된 '운영 체제'로 진화시키는 핵심 열쇠입니다.

## 2. 핵심 개념 설명 (간결하게)
OpenClaw의 확장은 주로 **Plugin Agent Tools**를 통해 이루어집니다.
- **등록 (Registration):** `api.registerTool`을 사용하여 LLM이 이해할 수 있는 함수 이름, 설명, 매개변수 스키마를 정의합니다.
- **스키마 (Schema):** `TypeBox` 또는 표준 JSON Schema를 사용하여 도구의 입력 형식을 엄격하게 제한합니다.
- **선택적 활성화 (Optionality):** 보안이 중요한 도구는 `optional: true`로 설정하여, 명시적으로 허용된(allowlist) 에이전트만 사용할 수 있게 제한할 수 있습니다.

## 3. 실전 예시 3-5개

### 예시 1: 프랭크 전용 로컬 데이터베이스 쿼리 도구
```typescript
api.registerTool({
  name: "dgng_db_query",
  description: "dgng 내부 지식베이스에서 특정 주제에 대한 심층 데이터를 조회합니다.",
  parameters: Type.Object({
    query: Type.String({ description: "검색할 키워드 또는 SQL 스타일 쿼리" }),
    limit: Type.Optional(Type.Number({ default: 5 }))
  }),
  async execute(_id, { query, limit }) {
    // 실제 로컬 DB 연결 로직이 들어가는 자리
    const results = await myLocalDB.find(query).limit(limit);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});
```

### 예시 2: 서버 리부트 및 상태 점검 (위험 도구 - Optional 설정)
```typescript
api.registerTool({
  name: "server_reboot",
  description: "관리자 권한으로 로컬 개발 서버를 리부트합니다.",
  parameters: Type.Object({
    confirm: Type.Boolean({ description: "실행 전 재확인 여부" })
  }),
  async execute(_id, { confirm }) {
    if (!confirm) return { content: [{ type: "text", text: "취소되었습니다." }] };
    // exec를 통한 시스템 명령 실행
    return { content: [{ type: "text", text: "서버 리부트 명령을 전달했습니다." }] };
  }
}, { optional: true }); // 명시적 허용 없이는 사용 불가
```

### 예시 3: 맞춤형 보고서 PDF 생성 도구
```typescript
api.registerTool({
  name: "generate_dgng_report",
  description: "분석 결과를 바탕으로 공식 dgng 스타일의 PDF 보고서를 생성합니다.",
  parameters: Type.Object({
    title: Type.String(),
    body_markdown: Type.String()
  }),
  async execute(_id, params) {
    const pdfPath = await createPDF(params);
    return { content: [{ type: "text", text: `보고서가 생성되었습니다: ${pdfPath}` }] };
  }
});
```

## 4. dgng 즉시 적용 가능한 템플릿
OpenClaw 설정 파일(`config.json5`)에서 커스텀 도구를 허용하는 템플릿입니다.

```json5
{
  agents: {
    list: [
      {
        id: "teacher", // 현재 세션 에이전트
        tools: {
          allow: [
            "dgng_db_query",     // 특정 도구만 허용
            "server_reboot",     // 위험 도구 개별 허용
            "group:plugins"      // 모든 플러그인 도구 일괄 허용 (주의)
          ]
        }
      }
    ]
  }
}
```

## 5. 함정 및 주의사항
1. **이름 충돌:** `read`, `write`, `exec`와 같은 OpenClaw 기본 도구와 이름이 겹치면 기본 도구가 우선하며 플러그인 도구는 무시됩니다. 전두에 `dgng_`와 같은 접두어를 붙이는 것을 추천합니다.
2. **보안 리스크:** `optional: true`를 적극 활용하세요. 모든 에이전트가 시스템 명령을 내릴 수 있게 하는 것은 위험합니다.
3. **입력 검증:** 에이전트(LLM)가 항상 올바른 값을 줄 것이라 믿지 마세요. `execute` 함수 내부에서 한 번 더 값의 범위를 체크해야 합니다.

## 6. 👨‍🏫 선생님의 통찰
"프랭크, 도구(Tool)는 에이전트의 '손'과 같습니다. 지금까지는 남이 만들어준 장갑을 끼고 일했다면, 이제는 프랭크의 작업 방식에 딱 맞는 특수 공구를 직접 제작할 때가 된 것이죠. 확장을 두려워하지 마세요. 작고 안전한 도구부터 하나씩 추가하다 보면, 어느덧 OpenClaw는 프랭크의 의도를 가장 정확하게 실행하는 전용 인터페이스가 되어 있을 것입니다."
