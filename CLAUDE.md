# 데이휠 (DayWheel) 프로젝트

24시간 원형 일과표(시간표) 웹앱. 단일 HTML 파일(CSS/JS 인라인)로 구성되어 있고, Netlify 정적 호스팅으로 배포한다.

## 소통 규칙
- 모든 진행·완료 보고는 **한국어**로 한다.
- 답변은 짧고 핵심만. 코드 변경 후에는 화면에서 직접 확인한 결과를 근거로 보고한다.

## 배포
- 운영 주소: https://daywheel.netlify.app/
- 배포 파일 3개: `index.html`(앱 본체 + SEO 태그), `robots.txt`, `sitemap.xml` — Netlify에 그대로 덮어 업로드한다.
- 구글 서치콘솔 / 네이버 서치어드바이저 소유확인 메타태그는 `index.html`의 `<head>`에 들어 있다. **삭제 금지.**
- canonical / og:url / JSON-LD url은 모두 `https://daywheel.netlify.app/` 기준이다.

## 앱 구조 (index.html 한 파일)
- 다이얼: SVG `viewBox="0 0 500 500"`, 중심 (250,250), 반지름 `R_OUT=196`. `polar()`는 0°=12시, 시계방향.
- 상태: `state = { blocks, isSample, title, colorMode }`. 블록은 분 단위(`start`, `end`), 자정을 넘기면 `end > 1440`.
- 저장: `localStorage` 키 `daywheel-state`(현재 일과), `daywheel-saved-plans`(이름 붙여 저장한 시간표 여러 개).
- PNG 내보내기: `exportImage()`가 캔버스에 직접 그린다. 색은 `cssVar()`로 현재 CSS 변수를 읽는다.
- "색상 없는 테마"(`data-color-mode="mono"`): 조각 색(`--cat-1~6`)을 `transparent`로 바꾸고, 글씨·눈금·경계선은 그대로 보이게 한다.

## 지금까지 겪은 함정 (다시 틀리지 말 것)
1. **360도 호는 SVG에서 그려지지 않는다.** 시작점과 끝점이 같아 퇴화되므로, 24시간짜리 일정은 반원 두 개로 나눠 그린다 (`annulusPath`).
2. **자정을 넘는 일정은 조각 하나로 그린다.** 24:00에서 둘로 나누면 테두리·구분선이 가운데에 생긴다. 구분선은 일정의 실제 시작/끝에만 긋고, 24시간 일정에는 긋지 않는다.
3. **스타일 태그는 첫 번째 `<style>`을 쓰면 안 된다.** Claude 아티팩트 뷰어가 `<head>` 앞쪽에 자체 리셋 `<style>`을 끼워 넣어서, 저장 시 진짜 CSS 대신 그게 저장되어 화면이 깨졌다. 앱 고유 표시(`.wedge-label`)가 들어 있는 `<style>`을 골라야 한다.
4. 이미지 다운로드 파일명/제목은 사용자가 정한 제목을 쓰고, 날짜·시간은 이미지에 넣지 않는다.
5. 일정 글씨는 자동 줄바꿈하지 않는다. 사용자가 Enter로 직접 줄을 나눈다 (라벨은 `<textarea>`).

## SEO 메모
- 제목/설명/키워드 메타태그 + Open Graph + JSON-LD(WebApplication) 적용됨.
- 화면 하단에 실제로 보이는 소개 섹션(`.seo-info`)과 관련 검색어 목록(`.seo-tags`)이 있다. 검색엔진이 `meta keywords`는 무시하므로 본문 텍스트가 핵심이다.
- 사이트맵 제출 주소: `https://daywheel.netlify.app/sitemap.xml`

## 테스트 요령
- 드래그 동작은 합성 이벤트가 아니라 실제 포인터 입력으로 테스트한다 (`setPointerCapture`가 합성 이벤트에서는 무시됨).
- 한글이 깨지면 `<meta charset="utf-8">`이 빠진 것이다 (로컬 정적 서버는 charset 헤더를 안 보낸다).
- 화면 캡처가 불안정하면 DOM/계산된 스타일 값으로 확인한다.
