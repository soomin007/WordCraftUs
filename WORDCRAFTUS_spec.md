# Word, Craft, Us — 구현 스펙

> Claude Code에게: 이 문서는 게임 "Word, Craft, Us"의 전체 기획과 구현 지침입니다.
> HTML/JS 단일 파일로 제작하며, GitHub Pages 배포를 전제로 합니다.

---

## 1. 게임 개요

**장르**: 내러티브 월드빌딩 / 소셜 실험  
**플랫폼**: 브라우저 (HTML + Vanilla JS, 단일 파일)  
**배포**: GitHub Pages  
**API**: Google Gemini API (gemini-2.5-flash), 키는 `config.js`에서 로드

### 핵심 컨셉
플레이어가 단어 몇 개를 던지면 "파트너"가 그것으로 세계를 묘사하고 확장한다.
파트너는 온라인 매칭으로 연결된 사람처럼 보이지만, 사실은 AI다.
플레이어는 공유를 선택하는 순간에야 이 사실을 알게 된다.

### 감정 목표
- 세계를 만드는 창조의 쾌감
- 파트너와 함께 만든다는 유대감
- 공유 순간의 작은 반전과 여운
- "AI가 만든 세계지만, 내가 만든 세계이기도 하다"는 인식

---

## 2. 게임 흐름

```
[시작] API 키 입력
  ↓
[매칭 화면] "파트너를 찾는 중..." (2-3초 딜레이)
  ↓
[파트너 등장] 중성적 이름 + 첫 인사
  ↓
[세션 1] 장소 만들기
  ↓
[세션 2] 사람 만들기
  ↓
[세션 3] 사건 만들기
  ↓
[세션 4] 유물 만들기
  ↓ (각 세션 후 분기)
[계속 확장] → 다음 세션으로
[공유하기] → 반전 고지 → 공유 카드 생성
```

---

## 3. 화면별 상세 스펙

### 3-1. 시작 화면
- 제목: **Word, Craft, Us**
- 부제 없음 (깔끔하게)
- "시작하기" 버튼만 있음 (키 입력 필드 없음)
- API 키는 `config.js`에서 자동 로드

### 3-2. 매칭 화면
```
파트너를 찾는 중...
[██████░░░░] 
```
- 프로그레스 바 애니메이션 (2.5초)
- 완료 시: "파트너를 찾았습니다." → 0.8초 후 전환

### 3-3. 파트너 등장
- 파트너 이름: 게임 시작 시 랜덤 선택
  - 후보: `Kai`, `Echo`, `Miro`, `Lena`, `Sable`, `Ren`
- 파트너의 첫 메시지 (고정 텍스트, API 호출 없음):
  > *"안녕하세요. 저도 처음이에요. 우리 같이 세계를 하나 만들어볼까요? 먼저 — 어떤 장소를 만들고 싶어요? 떠오르는 단어를 몇 개 던져줘요."*
- 타자기 효과로 한 글자씩 출력

### 3-4. 세션 화면 (공통 구조)
각 세션은 동일한 UI 패턴을 사용:

**레이아웃**:
- 왼쪽: 대화 로그 (파트너 메시지 + 내 입력 히스토리)
- 오른쪽: 현재 세계 상태 패널 (누적된 묘사들)
- 하단: 단어 입력창 + 전송 버튼

**입력 방식**:
- 단어를 쉼표 또는 스페이스로 구분하여 입력
- 예: `안개, 등대, 녹슨` 또는 `안개 등대 녹슨`
- 최소 1개, 최대 5개 단어 (초과 시 안내)
- Enter로 전송

**파트너 응답 연출**:
- 전송 후 "Kai가 입력 중..." 표시 (실제 API 응답 대기)
- 응답 도착 후 타자기 효과로 출력
- 배경색과 폰트가 응답의 무드에 맞게 서서히 전환 (CSS transition)

**세션별 파트너 프롬프트**:
- 세션 1 끝: *"좋아요. 이 장소가 생겼어요. 여기에 누가 살고 있을까요?"*
- 세션 2 끝: *"그 사람이 보여요. 이 장소에서 어떤 일이 있었을까요?"*
- 세션 3 끝: *"무언가 흔적이 남았을 것 같아요. 이 세계에 남겨진 물건이 있다면요?"*
- 세션 4 끝: → 분기 화면으로

### 3-5. 세션 분기 화면 (각 세션 후)
```
이 세계를 어떻게 할까요?

[계속 만들기]     [공유하기]
```
- 세션 1-3 후에는 둘 다 선택 가능
- 세션 4 후에는 반드시 분기 (더 추가할 레이어 없음)
- "계속 만들기" → 다음 세션으로
- "공유하기" → 반전 화면으로

### 3-6. 반전 화면
```
잠깐, 말할 게 있어요.

저는 사람이 아니에요.
Kai는 AI입니다.

그래도 — 이 세계는 진짜예요.
당신이 던진 단어들이 만든 세계니까요.
```
- 텍스트가 천천히 페이드인
- 하단: "공유 카드 만들기" 버튼

### 3-7. 공유 카드
- 세계의 누적된 묘사를 한 화면에 배치
- 색감은 세션 전체에서 쌓인 무드의 혼합
- 상단: "Word, Craft, Us"
- 본문: 장소 / 사람 / 사건 / 유물 요약 (각 1-2줄)
- 하단: "Kai와 함께 만든 세계"
- "이미지로 저장" 버튼 (html2canvas 사용)

---

## 4. Gemini API 연동

### API 호출 구조
```javascript
const response = await fetch(
  `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${apiKey}`,
  {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      contents: [{ parts: [{ text: prompt }] }],
      generationConfig: {
        responseMimeType: "application/json"
      }
    })
  }
);
```

### 응답 형식 (JSON)
Gemini에게 항상 아래 형식으로만 응답하도록 프롬프트에 명시:
```json
{
  "narrative": "2-4문장의 세계 묘사 텍스트 (한국어)",
  "partner_message": "파트너가 플레이어에게 건네는 한 마디 (한국어, 따뜻하고 호기심 있는 톤)",
  "mood": {
    "primary_color": "#2C3E50",
    "secondary_color": "#ECF0F1",
    "font_tone": "serif",
    "keywords": ["고요한", "쓸쓸한", "낡은"]
  }
}
```

### font_tone 값과 매핑
- `"serif"` → 'Noto Serif KR' (고요하고 문학적)
- `"mono"` → 'Noto Sans Mono' (냉정하고 기술적)
- `"sans"` → 'Noto Sans KR' (현대적, 중립적)
- `"display"` → 'Gaegu' (감성적, 손글씨 느낌)

### 시스템 프롬프트 (매 호출 시 포함)
```
You are Kai, a creative writing partner helping a user build a fictional world together.
The user is building a world layer by layer: Place → Person → Event → Relic.

Current world context:
{이전 세션들의 narrative 누적}

Current layer: {현재 세션 이름}
User's keywords: {입력된 단어들}

Respond ONLY in JSON format with this exact structure:
{
  "narrative": "2-4 sentences describing this layer in Korean, poetic and immersive",
  "partner_message": "One sentence from Kai to the player in Korean, warm and curious tone",
  "mood": {
    "primary_color": "hex color that fits the narrative atmosphere",
    "secondary_color": "contrasting hex color for text/accents",
    "font_tone": "serif | mono | sans | display",
    "keywords": ["분위기 키워드1", "키워드2", "키워드3"]
  }
}
Do not include any text outside the JSON.
```

---

## 5. 데이터 구조 (localStorage)

```javascript
const worldData = {
  partnerName: "Kai",
  sessions: [
    {
      layer: "장소",       // "장소" | "사람" | "사건" | "유물"
      keywords: ["안개", "등대", "녹슨"],
      narrative: "...",    // Gemini 응답
      mood: { ... }        // Gemini 응답
    },
    // ... 최대 4개
  ],
  createdAt: "2026-06-01T12:00:00"
}
```

- 키: `"wordcraftus_world"`
- 세션 종료(공유 선택) 후에는 데이터 유지 (다시 돌아와서 볼 수 있도록)
- "새 세계 만들기" 버튼으로만 초기화

---

## 6. 비주얼 / UI 가이드

### 전체 톤
- 어둡고 조용한 베이스 (배경 #0D0D0D 또는 #111)
- 텍스트는 오프화이트 (#F5F0E8)
- 색감 변화는 Gemini 응답의 `primary_color`로 배경에 은은하게 반영
  - CSS: `background: linear-gradient(to bottom, #0D0D0D, {primary_color}22)`
  - transition: 2초

### 폰트
- 제목: 'Playfair Display' (영문) + 'Noto Serif KR' (한글)
- 본문: Gemini 응답의 `font_tone`에 따라 동적 변경
- Google Fonts CDN 사용

### 매칭/로딩 UI
- 모노스페이스 폰트
- 터미널 느낌의 프로그레스 바 (`[████░░░░]`)
- 커서 깜박임 애니메이션

### 파트너 메시지
- 좌측에 파트너 이름 표시: `Kai >`
- 타자기 효과 (15ms/글자)
- 플레이어 입력은 우측 정렬, 흐린 색상

### 애니메이션
- 화면 전환: 페이드 (opacity transition, 0.6초)
- 배경색 변화: 2초 transition
- 타자기: requestAnimationFrame 또는 setInterval

---

## 7. 기술 스택

- **언어**: HTML5 + Vanilla JavaScript (단일 .html 파일)
- **스타일**: CSS (파일 내 `<style>` 태그)
- **외부 라이브러리**:
  - Google Fonts (CDN)
  - html2canvas (공유 카드 이미지 저장용, CDN)
- **API**: Google Gemini API (gemini-2.5-flash)
- **저장소**: localStorage (세계 데이터)
- **API 키 관리**: `config.js` 파일로 분리. 이 파일은 `.gitignore`에 추가하여 레포에 올라가지 않음.
- **배포**: GitHub Pages (정적 파일)

---

## 8. 에러 처리

- API 키 오류: "키를 다시 확인해주세요" 인라인 메시지
- API 호출 실패: "Kai가 잠시 응답하지 않아요. 다시 시도해볼게요." + 재시도 버튼
- 빈 입력: 전송 버튼 비활성화
- 단어 5개 초과: "단어는 5개까지만 입력할 수 있어요" 인라인 안내

---

## 9. 구현 우선순위

1. API 키 입력 + Gemini 연동 확인 (가장 먼저 검증)
2. 매칭 화면 + 파트너 등장 UI
3. 세션 1 (장소) 완전 구현 — 입력 → API → 응답 → 색감 변화
4. 세션 2-4 반복 구현
5. 분기 화면 + 반전 화면
6. 공유 카드 + html2canvas 저장
7. localStorage 누적 및 재방문 처리

---

## 10. 파일 구조

단일 파일: `index.html`
- `<head>`: 폰트, 메타태그
- `<style>`: 전체 CSS (CSS 변수로 무드 색상 관리)
- `<body>`: 화면별 섹션 (display:none으로 전환)
- `<script>`: 게임 로직 전체

GitHub Pages 배포 시 repo 루트에 `index.html` 하나만 있으면 됨.

추가 파일:
- `config.js`          ← `.gitignore`에 추가 (실제 키 포함, 레포 비공개)
- `config.example.js`  ← 레포에 올라감 (키 없음, 형식만 명시)
- `index.html`         ← `<script src="config.js">` 로 키 로드

`config.js` 형식:
```javascript
const GEMINI_API_KEY = "여기에_실제_키_입력";
```

`config.example.js` 형식:
```javascript
const GEMINI_API_KEY = "YOUR_API_KEY_HERE";
```
