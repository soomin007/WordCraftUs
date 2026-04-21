# Word, Craft, Us — 프로젝트 지침

## 프로젝트 개요

브라우저 기반 내러티브 월드빌딩 게임. 단일 `index.html` + Vanilla JS + Gemini 2.5 Flash. GitHub Pages로 배포.

- 라이브: https://soomin007.github.io/WordCraftUs/
- 레포: https://github.com/soomin007/WordCraftUs
- 스펙 원본: `WORDCRAFTUS_spec.md`

## 파일 구조

```
index.html            ← 전체 게임 (HTML + 내장 CSS + JS)
config.js             ← 실제 API 키 (.gitignore 됨, 레포에 없음)
config.example.js     ← 키 형식만 공개
.github/workflows/
  deploy.yml          ← 푸시 시 시크릿으로 config.js 생성 후 Pages 배포
WORDCRAFTUS_spec.md   ← 기획/구현 스펙
session_logs/         ← 세션 작업 로그 (아래 규칙 참고)
```

## API 키 관리 규칙

- **절대 `config.js`를 커밋하지 않는다.** `.gitignore`에 명시되어 있음.
- 배포 시 키는 GitHub repo secret `GEMINI_API_KEY`에서 Actions가 주입한다.
- 키를 교체할 때는 repo secret만 업데이트하면 됨. 코드 수정 불필요.
- 로컬 개발 시에만 `config.js`에 실제 키를 넣는다.

## 코드 컨벤션

- **단일 파일 원칙**: 기능 추가 시 `index.html`을 분리하지 않는다. CSS/JS는 `<style>` / `<script>` 태그 안에 유지.
- **한국어 UI**: 게임 내 모든 사용자 대면 텍스트는 한국어. 영문 혼용이 필요하면 파트너 이름(`Kai`, `Echo` 등)이나 제목처럼 의도된 경우만.
- **Gemini 응답 형식 준수**: 반드시 JSON 모드로 호출하고 `{narrative, partner_message, mood}` 구조를 검증한다. 구조가 깨지면 null 반환 후 재시도 UI 표시.

## 세션 로그 작성 규칙 (중요)

**매 세션이 끝날 때마다 그 세션의 핵심 작업·결정·변경을 `session_logs/YYYY-MM-DD.md`로 저장한다.**

- 파일명: `session_logs/YYYY-MM-DD.md` (오늘 날짜 기준 ISO 형식)
- 같은 날짜에 이미 파일이 있으면 **새로 만들지 말고 기존 파일에 이어서 추가** (시간대나 `## 세션 2` 같은 구분자로)
- 포함할 내용:
  - **요약**: 이 세션에서 무엇을 했는지 2-3줄
  - **주요 변경**: 파일 단위로 추가/수정된 것 bullet list
  - **결정 사항**: 구조·방식에 대해 내린 판단과 그 이유 (나중에 "왜 이렇게 했지?" 추적용)
  - **미해결 / 다음 할 일**: 남은 과제, 확인 필요한 것
- 길이는 보통 20-60줄 사이. 너무 짧으면 의미 없고, 너무 길면 유지가 안 됨.
- 디버깅 로그나 실패한 시도는 원인과 함께 짧게 기록 (같은 실수 반복 방지)
- 세션 로그는 레포에 커밋한다 (`session_logs/`는 `.gitignore`하지 않음).
