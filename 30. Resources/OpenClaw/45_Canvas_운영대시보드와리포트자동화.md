# 45. Canvas: 운영 대시보드·리포트 자동화

## 1) 왜 중요한가 (dgng 운영 맥락)
- dgng 운영은 “지금 상태”를 **빠르게 공유**하는 게임입니다.
- 텍스트 요약은 해석/누락이 생기기 쉬운데, Canvas는 상태를 **한 장의 이미지(증거)** 로 고정합니다.
- 결과:
  - 점검/장애 상황을 팀과 같은 화면으로 보고
  - 주간 리포트를 자동으로 누적 아카이빙하고
  - 승인(“OK/NO”)을 빠르게 받을 수 있습니다.

## 2) 핵심 개념
- **present**: 캔버스 열기(렌더 영역 확보)
- **navigate**: URL 또는 file:// 로 이동
- **eval**: JS로 데이터 주입/DOM 갱신
- **snapshot**: 화면을 png/jpg로 캡처(리포트의 실체)

## 3) 실전 예시 4개

### 예시 1) HTML 템플릿 렌더 → 스냅샷
```js
await canvas({action:"present", width:1000, height:650})
await canvas({action:"navigate", url:"file:///Users/frank/.../ops.html"})
await canvas({action:"snapshot", outputFormat:"png", maxWidth:1200})
```

### 예시 2) 점검 결과만 교체(같은 레이아웃 재사용)
```js
const s={service:"dgng", cpu:"23%", queue:"OK", incident:"none"}
await canvas({action:"eval", javaScript:`window.setStatus(${JSON.stringify(s)})`})
```

### 예시 3) 노드 점검 → 대시보드에 합치기(개념)
- `nodes.run`으로 현장 지표 수집
- 수집 JSON을 `canvas.eval`로 주입
- `snapshot`을 텔레그램에 첨부

### 예시 4) 주간 리포트 이미지 품질 튜닝
```js
await canvas({action:"snapshot", outputFormat:"jpg", quality:85, maxWidth:1600})
```

## 4) dgng 즉시 적용 템플릿
- **ops.html**(최소)
```html
<div id="root">loading…</div>
<script>
window.setStatus=(s)=>{
  root.innerHTML=`<b>${s.service}</b><br/>cpu:${s.cpu}<br/>queue:${s.queue}<br/>incident:${s.incident}`
}
</script>
```
- **런북**
  1) nodes.run 점검
  2) canvas.present/navigate
  3) canvas.eval(JSON 주입)
  4) canvas.snapshot 저장(날짜 파일명)
  5) message.send로 이미지+한 줄 코멘트

## 5) 함정/주의
- `file://` 경로, 공백/한글 경로에서 삑사리: **절대경로 고정** 후 테스트.
- 레이아웃 흔들림: width/height/maxWidth를 **항상 동일**하게.
- eval 문자열 깨짐: `JSON.stringify`로 주입하는 패턴을 고정.
- 한 장에 너무 많이 넣지 말기: **핵심 5지표**만.

## 6) 👨‍🏫 선생님의 통찰
- 운영의 본질은 “정답”이 아니라 **공유 가능한 상태(Shared Reality)** 입니다.
- Canvas 스냅샷을 매일 쌓으면, 장애 시 “어제 화면 vs 오늘 화면” 비교가 가장 빠른 원인 단서가 됩니다.
