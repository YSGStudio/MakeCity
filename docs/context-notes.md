# Context Notes: village-builder-3d

## 핵심 파일
- `village3d.html` — 게임 전체(HTML + CSS + 모듈 JS, 약 1,040줄). 별도 빌드나 패키지 없음.
- `village3d_v1.html` — 들 마을을 추가하기 전 버전(바다·산 2개 마을). 비교·되돌리기용.
- 배포: claude.ai 아티팩트 slug `670a3bc4-476f-4f33-bcbb-ac9218715d81`
  (`https://claude.ai/artifact/Diyv4HoPs76ZneMaM1STmz`), `publish_files`로 `index.html` 하나를 올림.

## 외부 의존성
- import map: `three` → `https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js`,
  `three/addons/` → `.../examples/jsm/` (OrbitControls 사용)
- Google Fonts: Jua(제목), Noto Sans KR(본문)
- 아티팩트 보안 정책상 스크립트는 cdnjs, jsDelivr(/npm), unpkg 등에서만, 이미지는 data:/blob:만 불러올 수 있음.
  alert/confirm은 막혀 있으므로 자체 모달(`#modal`)과 토스트(`#toast`)를 씀.

## 코드 관례
- 좌표: 칸 (x, z)는 0..19, 월드 좌표는 `wx(x) = x - N/2 + 0.5`. 칸 윗면 높이 = `t.h`(물 칸은 음수).
- 지형 블록은 공용 `unitBox`(바닥이 y=0)를 `scale.y = h+1`로 늘려 y=-1부터 세움.
- 재질은 색 문자열을 키로 `mat()`에 캐시. 모델 도우미 `box/cone/cyl(..., x, y, z)`의 y는 **바닥 기준**.
- 건물 그룹의 원점은 칸 윗면 중심. 물 위에 놓이는 부분(항구 부두, 배, 다리)은 로컬 y를 `-t.h`로 보정.
- 애니메이션은 그룹의 `userData.anim(dt, time)`에 넣으면 렌더 루프가 현재 마을 그룹의 자식만 호출함.
- `check()`가 입지 규칙의 유일한 출처. 결과의 `hint` 키는 `HINTS`, `variant`는 모델 분기(논/계단식 밭, 해수욕장/스키장/놀이공원)에 쓰임.
- 마을별 이름이 다른 도구(관광지)는 `toolName`, `toolIcon`, `BNAME`을 거쳐 표시.
- 함수 선언 순서 주의: `makeMap()` 안에서 `addLog()`가 `cur()`를 부르므로 `curType`·`cur`는 `maps` 생성보다 먼저 선언해야 함(한 번 이 순서 때문에 초기화 오류가 났었음).

## 새 마을/시설을 추가할 때 고칠 곳
- 마을: `genTerrain` 분기, `makeRoadPlan` 분기(+`M.cx/cz`), `TERRAIN_DESC`, `ERA.<type>`(13개),
  `MISSIONS.<type>`, `START_PRESET`, `PLAN`, `COLORS`, `MNAME`, 탭 버튼과 `.tabs button.on.<type>` CSS,
  `switchMap` 배경색, `maps.<type> = makeMap(...)`, 비교 창 목록.
- 시설: `TOOLS`(group:'city'면 도시 시설 구분선 아래), `check` case, `buildMesh` case,
  `calc`의 카운터·일자리·환경, `simulate`의 수입·유지비, `HINTS`, `autoHint`, 비교 표 `bl`.

## 검증 방법(지금까지 사용)
- `/home/claude/t/`에서 `npm i three@0.160.0` 뒤 CDN 주소를 `./node_modules/three/`로 바꾼 `test.html` 생성.
- Python Playwright + Chromium(`--use-gl=swiftshader --enable-webgl --ignore-gpu-blocklist`)으로
  `pageerror` 확인, 스크린샷, `#btn-auto` 자동 진행 후 `#modal-body` 텍스트 읽기.
- 최근 자동 발전 결과(2030): 바다 인구 367·환경 81, 산 117·100, 들 1,006·56(고층빌딩 12, 아파트 32).

## 저장 방식 결정 (사용자 지시, 2026-11-11)
- 데이터베이스는 만들지 않는다. 한 차시 안에서 끝내는 활동이다.
- 간단한 데이터만 브라우저 `localStorage`에 저장한다. 키는 `village3d:v1:<type>`.
- 현재 구현(v2)에는 아직 저장 기능이 없다. PRD의 R21~R27과 T14~T16이 새로 구현할 범위다.
- 지형은 `genTerrain`이 결정적이므로 저장하지 않는다. 건물은 `{x,z,b,variant}`만 저장하고, 불러올 때 `place(M,b,tile,{variant},true)`로 다시 짓는다
  (이때 `M.ready=false`로 두면 애니메이션·힌트가 나오지 않는다).
- 아티팩트 안에서도 localStorage는 동작하지만, 보는 사람의 브라우저에만 남고 예외가 날 수 있으므로 반드시 try/catch로 감싼다.
- `alert/confirm`이 막혀 있으므로 다시 시작 확인은 기존 `#modal`을 재사용한다.
