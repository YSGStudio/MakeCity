# 우리 마을 가꾸기 3D

바다 마을·산 마을·들 마을을 직접 가꾸며 지형에 따라 마을이 어떻게 발달하는지 알아보는 초등 사회 수업용 3D 게임입니다.

- 설치나 빌드 없이 `public/index.html` 파일 하나로 동작합니다. (three.js는 jsDelivr CDN에서 불러옵니다.)
- 진행 상황은 학생 브라우저의 localStorage에 자동 저장됩니다. 서버나 DB는 없습니다.

## 폴더 구조

| 경로 | 설명 |
|---|---|
| `public/index.html` | 게임 본체 (배포되는 유일한 파일) |
| `docs/` | PRD, 체크리스트, 메모, v2 백업 (배포되지 않음) |
| `vercel.json` | Vercel 설정: 빌드 없이 `public/` 폴더만 배포 |

## 로컬에서 실행

```bash
npm run dev        # 또는 python3 -m http.server 8000 -d public
# 브라우저에서 http://localhost:8000 접속 (끄려면 Ctrl+C)
```
설치할 라이브러리가 없어 `npm install`은 하지 않아도 됩니다.

## 배포 (GitHub → Vercel)

1. 이 폴더를 GitHub 저장소에 올립니다.
2. vercel.com → **Add New… → Project** → 저장소 선택 → **Deploy**.
   설정은 `vercel.json`에 들어 있어 따로 바꿀 것이 없습니다.
3. 이후 `main` 브랜치에 push하면 자동으로 다시 배포됩니다.
