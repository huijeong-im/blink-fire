# Finger Shooter

단일 HTML 파일 게임. 빌드 도구 없음 — 브라우저에서 바로 열면 실행됨.

## 실행 방법

```
open index.html
```

## 기술 스택

- **MediaPipe Face Mesh** (CDN) — 얼굴/눈 추적
- **HTML5 Canvas** — 픽셀아트 렌더링
- 외부 의존성 없음, 서버 불필요

## 조작 방법

- 고개를 움직여 조준
- 눈 깜빡이면 발사 (빠른 깜빡임)
- 눈 오래 감으면 (0.8초+) 시작/재시작
- 마우스 클릭으로도 테스트 가능

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
| MEDIAPIPE | Face Mesh 초기화 + EAR 깜빡임 감지 + 코끝 추적 |

## 주요 파라미터 (조정 가능)

```js
BLINK_THRESH = 0.18   // 눈 감음 판정 임계값 (낮추면 덜 민감)
SMOOTH       = 0.15   // 조준선 EMA 스무딩 (낮을수록 부드러움)
FACE_SENS_X  = 2.2    // 좌우 감도
FACE_SENS_Y  = 2.0    // 상하 감도
```

## 적 종류

| 이름 | HP | 점수 | 속도 |
|------|----|------|------|
| slime | 1 | 10×wave | 느림 |
| bat | 1 | 15×wave | 빠름 |
| skeleton | 3 | 30×wave | 느림 |
