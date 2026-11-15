# WCRC 2026 물류로봇2-분류 (대학부)

메카넘 휠 모바일 매니퓰레이터가 미션 블록의 순서를 카메라로 읽고, 적재함에서 블록을 찾아 집어 미션수행 Zone A·B의 지정 칸에 분류하는 자율 로봇 펌웨어입니다.

> **🏆 2026 WCRC 물류로봇2-분류 대학부 경진대회 — 한국로봇교육콘텐츠협회장상**
> 주최: 한국기술교육대학교 · 대학혁신지원사업 / 주관: 한국로봇교육콘텐츠협회 · 로봇앤모어

## 경기 흐름

```
출발 → 장애물 라인 통과 → 미션지시 Zone에서 순서 읽기 → 적재함 1열 도착
  └→ [블록마다] 적재함 탐색 → 집기 → Zone A 또는 Zone B에 넣기 → 적재함 복귀
→ Finish
```

| 단계 | 하는 일 |
|---|---|
| 장애물 | 462mm 전진 후 전방 PSD 2개로 거리·비틀림 정렬 |
| 순서 읽기 | 팔을 왼쪽/오른쪽 두 자세로 돌려 6자리를 촬영, 램프 켬/끔 두 번 투표, 자리별 화면 범위로 색 결정, 못 읽은 자리는 배정표로 복원 |
| 적재함 탐색 | 지금 열부터 4열까지 갔다가 1열로 (3층 × 4열), 본 블록 위치를 기억해 다음 블록은 바로 이동 |
| 집기 | 카메라 x 오차만큼 옆으로 이동 → 접근 → 집기 → 빼기 |
| Zone A | 1열 왼쪽 기준점에서 SL PSD 정렬 후 8칸 자세로 낙하 → 1열로 복귀 |
| Zone B | 회전 2번으로 이동 → 정면 PSD 정렬 → 낙하 → 회전 직전 정렬 후 **2열로 바로 복귀** |

## 하드웨어

| 구성 | 사양 |
|---|---|
| 제어기 | Arduino Mega 2560 |
| 주행 | Dynamixel XL430 × 4 (ID 1~4), 메카넘 휠 |
| 팔 | Dynamixel XL430 × 4 (ID 5~8, 4자유도), 그리퍼는 Pixy2 서보 포트 |
| 센서 | Pixy2 카메라, PSD 4개 (A0 전방좌 · A1 측면좌 · A2 전방우 · A3 측면우) |
| 입력 | SW1 = 경기 시작(t7), SW2 = 정지 |

## 저장소 구조

```
T45/T45_Mission2B/      대회 최종 코드 (Arduino 스케치 폴더)
  T45_Mission2B.ino     경기 흐름, 순서 읽기, 탐색, Zone 이동, 시리얼 콘솔
  T45_Tuning.h          조정값 전부 (거리 · PSD 목표 · 속도 · 팔 자세 · 카메라 범위 · 배정표)
  T45_Mobilebase.*      바퀴 이동, PSD 정렬
  T45_Manipulator.*     팔 (확장 위치 모드, 관절 범위 창 계산)
  T45_Devices.*         PSD, 그리퍼
docs/                   설명 문서
CHANGES.md              버전별 변경 기록 (v6 ~ T45)
archive/                이전 버전 T25~T44, 강사 예제 복원본(INSTR), 옛 문서
```

## 빌드 · 업로드

1. Arduino IDE에서 `T45/T45_Mission2B/T45_Mission2B.ino`를 엽니다. 폴더 안 10개 파일이 함께 있어야 합니다.
2. 라이브러리: **Dynamixel2Arduino**, **Pixy2** (arduino_pixy2), **Adafruit NeoPixel**
3. 보드: Arduino Mega or Mega 2560 → 업로드
4. 시리얼 모니터 115200 → 첫 줄에 `< T45 >`가 나오면 정상

## 사용

- 경기: SW1 또는 시리얼 `t7`
- 값 바꾸기: `T45_Tuning.h`만 수정 후 재업로드 → [docs/TUNING.md](docs/TUNING.md)
- 현장 측정·시험 명령: [docs/CONSOLE.md](docs/CONSOLE.md)

## 문서

| 문서 | 내용 |
|---|---|
| [docs/DEVELOPMENT.md](docs/DEVELOPMENT.md) | 개발 과정과 핵심 문제 해결 사례 |
| [docs/PORTFOLIO.md](docs/PORTFOLIO.md) | 이력·포트폴리오용 요약 |
| [docs/TUNING.md](docs/TUNING.md) | Tuning.h 주요 값과 측정 방법 |
| [docs/CONSOLE.md](docs/CONSOLE.md) | 시리얼 명령 |
| [docs/AI_CONTEXT.md](docs/AI_CONTEXT.md) | 다른 AI 대화에 넣을 프로젝트 요약 |
| [CHANGES.md](CHANGES.md) | 버전별 변경 기록 |

남은 개선 과제는 [Issues](https://github.com/kongmingi/WCRC-2026/issues)에 정리되어 있습니다. 대회 당시 코드는 태그 `v1.0-competition`으로 고정했습니다.
