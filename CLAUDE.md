# BLINK FIRE 🔥

단일 HTML 파일 게임. 빌드 도구 없음 — 브라우저에서 바로 열면 실행됨.

## 실행 방법

```
open index.html
```

## 기술 스택

- **MediaPipe Face Mesh** (CDN) — 얼굴/눈 추적
- **HTML5 Canvas** — 픽셀아트 렌더링
- **Upstash Redis REST API** — 글로벌 리더보드
- 외부 의존성 없음, 서버 불필요

## 조작 방법

- 고개를 움직여 조준
- 눈 깜빡이면 발사 (빠른 깜빡임, 60~450ms)
- 코 찡긋해도 발사 (Z축 기반 감지)
- 눈 오래 감으면 (0.8초+) 시작/재시작
- 마우스/터치 클릭으로도 테스트 가능

## 코드 구조 (index.html 단일 파일)

| 섹션 | 내용 |
|------|------|
| FONT | 5×5 픽셀 폰트 글리프 정의 |
| SPRITES | 픽셀아트 스프라이트 (slime, bat, skeleton, heart) |
| GAME STATE | score, lives, wave, enemies, particles 등 |
| ENEMY CONFIG | 타입별 HP / 점수 / 속도 |
| EFFECTS | muzzleFlashes, bulletTrails, shockwaves, particles |
| UPDATE | 게임 로직 루프 (dt 기반) |
| DRAW | Canvas 렌더링 함수들 |
| LEADERBOARD | Upstash Redis REST API 연동 (submitScore, fetchLeaderboard) |
| MEDIAPIPE | Face Mesh 초기화 + EAR 깜빡임 감지 + 코끝 추적 + 코 찡긋 감지 |

## 주요 파라미터 (조정 가능)

```js
BLINK_THRESH  = 0.18   // 눈 감음 판정 임계값 (낮추면 덜 민감)
HEAD_SENS_X   = 2.2    // 좌우 감도
HEAD_SENS_Y   = 2.0    // 상하 감도
// 적응형 EMA 스무딩: smooth = min(0.85, 0.2 + dist/120)
// 빠른 움직임 → 빠르게 따라감, 미세 조정 → 부드럽게
```

## 리더보드 (Upstash Redis)

- `UPSTASH_URL` / `UPSTASH_TOKEN` — REST API 엔드포인트 + Bearer 토큰
- `LB_KEY = 'blinkfire:lb'` — Redis Sorted Set 키
- `ZADD GT` — 닉네임당 최고 점수만 유지
- `ZREVRANGE WITHSCORES` — 상위 15위 조회
- 게임 오버 시 자동 점수 제출, 리더보드 새로고침

## 닉네임

- 게임 시작 전 닉네임 입력 (HTML overlay)
- `localStorage`에 저장되어 다음 방문 시 자동 입력
- 닉네임은 리더보드 키로 사용 (같은 닉네임 = 같은 계정)

## 모바일 지원

- `getUserMedia({ facingMode: 'user' })` — 전면 카메라 사용
- 터치 이벤트 지원 (touchstart)
- `<meta viewport>` + CSS `min()` — 반응형 레이아웃

## 적 종류

| 이름 | HP | 점수 | 속도 |
|------|----|------|------|
| slime | 1 | 10×wave | 느림 |
| bat | 1 | 15×wave | 빠름 |
| skeleton | 3 | 30×wave | 느림 |
