# T45_Tuning.h 주요 값

값은 모두 `T45/T45_Mission2B/T45_Tuning.h` 한 파일에 있습니다. 바꾼 뒤 재업로드하면 됩니다. 아래 숫자는 대회 최종 값입니다.

## 값을 정하는 원칙
1. 로봇을 **바른 위치에 손으로 놓고** `psd`(또는 `t1`)를 2~3번 찍어 그 값을 목표로 넣는다.
2. 그 자리에서 ±5~20mm 움직여 보고 값이 **고르게 변하는지** 확인한다. 거의 안 변하면(센서 최고점 근처) 그 지점은 기준으로 쓰지 않는다.
3. 한 번에 하나만 바꾸고, **배터리 11.5V 이상**에서 시험한다.
4. PSD 값은 mm가 아니다. 클수록 가깝다.

## 배정표
```
missionAssignments[] = { { 색, ZA(칸) 또는 ZB(칸) }, ... }
```
색 번호: 빨1 주2 노3 초4 파5 보6. 대회 당일 공개되는 표를 그대로 넣는다. `zone` 명령으로 업로드 없이 바꿀 수도 있다.

## 속도
| 값 | 최종 | 쓰는 곳 |
|---|---|---|
| `MOVE_MM_PER_SEC` | 170 | 전진·후진 |
| `SIDE_MM_PER_SEC` | 170 | 좌진(순서 읽으러), B 복귀 전진 |
| `SEARCH_MM_PER_SEC` | 160 | 적재함 열 이동, 카메라 보정 |
| `ROTATE_MM_PER_SEC` | 100 | 90° 회전 (올리면 각도 오차 커짐) |
| `WHEEL_RATIO_*` | | 방향별 바퀴 4개 비율 (FL FR BL BR). 이동 중 휘거나 밀리면 조정 |

## PSD 목표 (구간별)
| 구간 | 값 | 최종 |
|---|---|---|
| 장애물 앞 | `PSD_OBSTACLE_AVG` / `_ROT_ADJUST` | 560 / −25 |
| 순서 읽는 자리 | `PSD_MISSION_SL` | 476 |
| 1열 왼쪽 기준점 | `PSD_DROPSIDE_REF_SL`, 벽 `PSD_SEARCH_WALL_AVG` / `_ROT_ADJUST` | 572, 182 / −3 |
| 열 앞뒤 | `PSD_STORAGE_FR` | 306 |
| 2·3열 비틀림 | `PSD_STORAGE_COL_ROT_ADJUST` | { 0, −3, −3, 0 } |
| Zone B 정면 | `PSD_DROPBOX_B_FRONT_AVG` / `_ROT_ADJUST` | 554 / −41 |
| B 복귀 회전 직전 | `PSD_RETURN_A_AVG` / `_ROT_ADJUST`, `PSD_RETURN_STORAGE_SR` | 219 / −43, 284 |
| B 복귀 회전 후 | `PSD_RETURN_STORAGE_ROT_ADJUST` | 0 |

`_ROT_ADJUST`는 바른 자세일 때 `psd`에 찍히는 **FR−FL 값 그대로**를 넣는다. 허용 오차는 `..._TOLERANCE`(대부분 2~3).

## 거리 (mm)
| 값 | 최종 | 뜻 |
|---|---|---|
| `mm_F_BLINKING_to_OBSTACLE` | 462 | 출발 → 장애물 |
| `mm_L_OBSTACLE_to_MISSION` / `mm_B_...` | 350 / 20 | 장애물 → 순서 읽는 자리 |
| `mm_F_MISSION_to_DROPBOX` | 858 | 순서 읽는 자리 → 적재함 쪽 |
| `mm_R_CELL1_to_CELL2` … | 60 / 70 / 70 | 오른쪽 열 간격 |
| `mm_L_CELL4_to_CELL3` … | 65 / 78 / 70 | 왼쪽 열 간격 |
| `mm_R_REF_to_CELL1` | 110 | 1열 기준점 → 1열 |
| `mm_F_TO_DROPBOX_B` / `mm_F_DROPBOX_B_to_CELL2` | 366 / 139 | Zone B 앞, B → 2열 |

## 카메라
| 값 | 최종 | 뜻 |
|---|---|---|
| `PIXY_SET_POINT_X[3]` | { 150, 169, 168 } | 층별 그리퍼 중심의 화면 x (1층부터) |
| `MIN/MAX_PIXY_SEARCH_X/Y` | x 78~238, y 52~145 | 적재함에서 블록으로 인정할 화면 범위 |
| `CAM_SEARCH_REPEAT_COUNT` / `CAM_SEARCH_MIN_HITS` | 3 / 1 | 층마다 찍는 장수 / 찾았다고 볼 최소 장수 |
| `CAM_X_ALIGN_MAX_TRY`, `CAM_ALIGN_RECHECK` | 1, false | 중심 보정 횟수, 보정 후 재확인 여부 |
| `STORAGE_LAMP_FOR_COLOR[7]` | 전부 1 | 적재함에서 색별 램프 (−, 빨, 주, 노, 초, 파, 보) |
| `SIG7_COLOR` | 0 | Pixy sig 7을 이 색으로 취급 (0 = 안 씀) |
| `ORDER_SLOT_X/Y_MIN/MAX[6]` | | 순서 읽기 6자리 화면 범위 (1~3 = orderL, 4~6 = orderR) |
| `ORDER_GUESS_IF_UNSURE` | true | 순서가 확정 안 되면 추측해서 진행 (false = 경기 중단) |

## 팔 자세
`POSE_TRAVEL`, `POSE_CARRY`, `POSE_DRIVE`, `POSE_ORDER_L/R`, `POSE_SCAN[3]`, `POSE_PICK_APPROACH[3]`, `POSE_PICK_GRIP[3]`, `POSE_ZONE_A[8]`, `POSE_ZONE_B[8]` — 각 `{ j1, j2, j3, j4 }` 모터 값.

잡는 법: `p 이름` → `j2 +30` 등으로 조정 → `save 이름` → 출력된 줄을 Tuning.h에 붙여 넣기.
⚠ 집기 자세(pa·pg)와 carry는 j1에 저장값 대신 `J1_FRONT`(정면)가 쓰인다. 좌우가 틀어지면 `J1_FRONT`나 카메라 중심 x를 고친다.
