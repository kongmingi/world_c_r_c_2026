# AI 대화용 프로젝트 요약

다른 AI 대화(이력서 작성, 코드 수정 등)를 시작할 때 이 파일을 붙여 넣으면 됩니다.

## 프로젝트
- 2026 WCRC 물류로봇2-분류 대학부 출전 로봇 펌웨어. 결과: **한국로봇교육콘텐츠협회장상**
- 나(저장소 주인)는 팀에서 소프트웨어 설계·구현, 센서·자세 실측과 튜닝, 시험 운영 등 개발 대부분을 주도했다. 코드 작성은 AI 코딩 도구(Claude)의 보조를 받았고, 요구사항·측정·튜닝·의사결정은 직접 했다.
- 기간: 2026년 10월 말 ~ 11월 14일
- 상세: [DEVELOPMENT.md](DEVELOPMENT.md), 이력 요약: [PORTFOLIO.md](PORTFOLIO.md)

## 하드웨어
Arduino Mega 2560, Dynamixel XL430 8개 (ID 1~4 바퀴 FL FR BL BR 메카넘, ID 5~8 팔 4자유도), Pixy2 (SPI, 그리퍼 서보 겸용), PSD 4개 (A0 FL, A1 SL, A2 FR, A3 SR), SW1(41) 시작, SW2(40) 정지, NeoPixel, 부저

## 경기장 (1800 × 760mm)
출발(좌하) → 장애물 라인(Zone B 옆) → 미션지시 Zone(상단, 블록 최대 6개 순서) → 적재함(우측, 3층 × 4열, 최하단 막힘) ↔ Zone A(상단, 8칸) / Zone B(하단 중앙, 8칸) → Finish. 2분, 블록당 10점, 순서 준수 20점, 장애물 라인 10점, 도착 10점.

## 코드 (최종: `T45/T45_Mission2B/`)
- `T45_Mission2B.ino`: `RunMission()`(경기), `ReadMissionSlots()`(순서 읽기·복원), `SearchLoop()` / `TryKnownCell()`(탐색·기억), `CamFindBlockAndPickup()`(층 스캔·x 보정·집기), `DropAndReturn()` / `GotoDropboxB()` / `ReturnFromDropboxBToCell2()` / `AlignFromDropside()`(Zone 이동·복귀), `PerformMoveRotateAlign()` 등 PSD 정렬, `Handle()`(시리얼 명령)
- `T45_Tuning.h`: 모든 조정값 ([TUNING.md](TUNING.md))
- `T45_Manipulator.cpp`: 확장 위치 모드 + `ToWindow()` 관절 창 계산. `RunManipulatorPoseWithPoseDataInEEPROM(id, ms, angle)`에서 angle ≠ −360이면 j1을 `J1_FRONT + angle(도→모터값 변환)`로 강제 (집기 자세·carry)
- `T45_Mobilebase.cpp`: 거리 이동(시간 초과 시 `Move wait timed out`), PSD 정렬 내부 함수
- 시리얼 명령: [CONSOLE.md](CONSOLE.md)

## 핵심 알고리즘 메모
- 순서 읽기: orderL(자리 1~3)·orderR(4~6) 자세에서 램프 켬/끔 각 5프레임, 자리별 화면 범위 안 가장 큰 블록에 투표, 3표 이상이면 확정 → 배정표 색 구성으로 과다 색 제거·누락 자리 복원(hits·weak로 빈자리 구분). 출력 `[ORDER] read/hits/weak/final|guess`
- 적재함: 홀수 열 위→아래, 짝수 열 아래→위 층 순서. 화면 범위 x 78~238, y 52~145. 블록 x 오차 × 0.48mm/px만큼 이동 후 집기
- Zone B 복귀: 후진 366 → 우회전 → 전진 139 → (Zone A 쪽) 정면 avg 219 / FR−FL −43 + SR 284 → 우회전 → FR 306 + 비틀림 0 → 2열부터 탐색
- 1열 정렬: 1열 왼쪽 110 기준점에서 SL 572 → 벽 182 / −3 → 오른쪽 110 → FR

## 알려진 함정
- PSD 600 근처는 평탄 구간이라 기준으로 부적합
- 배터리 9V대에서 이동·정렬 오차와 시간 초과 급증
- Pixy는 화소를 한 signature에만 배정 → 넓은 range의 색이 비슷한 색을 가져감. 화면 끝은 자동 노출 때문에 어둡게 찍힘
- 화면 범위(Tuning) 오타 한 줄로 전부 `out`이 되어 아무것도 못 찾은 적이 있음 → `t1`의 IN/out으로 확인
- 줄 끝 `;`가 지워지면 다음 줄 이름이 "was not declared in this scope"

## 작업 규칙 (이 프로젝트에서 써 온 방식)
- 코드에 주석을 넣지 않는다. 값은 Tuning.h에만, 코드 안 숫자 하드코딩 금지
- 코드 수정 전에 먼저 묻고, 한 번에 하나만 바꾼다. 측정하지 않은 값을 지어내지 않는다
- 새 버전은 `TNN/TNN_Mission2B/TNN_*.ino|.h|.cpp`(모든 파일에 접두어), 이전 버전은 `archive/`로, 변경은 `CHANGES.md`에 기록, GitHub에 push
- 답변은 한국어로 짧게. 사용자는 Tuning.h를 직접 고치고, 바꿀 줄을 알려 주면 된다
