# Word, Craft, Us

### [▶ 바로 플레이하기](https://soomin007.github.io/WordCraftUs/)

> 당신이 던진 단어로 세계가 태어난다. 파트너와 함께, 네 개의 레이어 위에.

---

## 게임 소개

**Word, Craft, Us**는 브라우저에서 플레이하는 짧은 내러티브 월드빌딩 게임입니다.

플레이어는 온라인으로 매칭된 "파트너"와 함께 단어 몇 개를 주고받으며 세계를 하나 만들어갑니다. 장소에서 시작해 사람, 사건, 유물까지 — 네 개의 레이어가 차곡차곡 쌓이고, 배경색과 글꼴은 그 세계의 무드에 따라 조용히 바뀝니다.

공유를 선택하는 순간, 작은 반전이 기다립니다.

## 흐름

```
시작  →  매칭  →  파트너 등장
  ↓
[세션 1] 장소  →  [세션 2] 사람  →  [세션 3] 사건  →  [세션 4] 유물
  ↓                                                         ↓
계속 만들기 ←──────────────────────────────── 공유하기
                                                           ↓
                                                       반전 / 공유 카드
```

## 기술 스택

- HTML + Vanilla JavaScript (단일 `index.html`)
- Google Gemini API (gemini-2.5-flash)
- html2canvas (공유 카드 이미지 저장)
- localStorage (세계 데이터 영속화)
- GitHub Pages (정적 호스팅) + GitHub Actions (시크릿 주입 배포)

## 로컬에서 실행하기

1. 레포 클론
   ```
   git clone https://github.com/soomin007/WordCraftUs.git
   cd WordCraftUs
   ```

2. `config.example.js`를 `config.js`로 복사하고 본인의 Gemini API 키를 입력
   ```
   cp config.example.js config.js
   ```
   ```js
   const GEMINI_API_KEY = "여기에_본인_키";
   ```

3. `index.html`을 브라우저로 열거나 간단한 로컬 서버로 서빙
   ```
   python -m http.server 8000
   ```

> `config.js`는 `.gitignore`로 제외되어 레포에는 올라가지 않습니다.

## 배포 구조

- `config.js`는 로컬 전용, 레포에 존재하지 않음
- 푸시 시 GitHub Actions가 repo secret `GEMINI_API_KEY`를 읽어 배포 artifact에 `config.js`를 생성
- 따라서 **레포 소스에는 키가 노출되지 않으면서도 라이브 사이트는 정상 동작**

## 라이선스

개인 프로젝트, MIT.
