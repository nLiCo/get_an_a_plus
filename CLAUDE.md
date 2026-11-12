# get_an_a_plus

강의계획서 3장 중 하나를 골라 15주 한 학기를 보내며, 숨겨진 평가 성향을 가진 가상의 교수에게 A+를 받아내는 웹 턴제 게임. 오픈소스소프트웨어 수업 개인 과제. GitHub Pages로 배포한다.

## 작업 시작 전에 반드시

1. `docs/SPEC.md`를 읽는다. 특히 "4. 범위"의 하지 않을 것 목록.
2. `docs/PROGRESS.md`에서 현재 마일스톤과 체크 안 된 첫 항목을 확인한다.
3. 그 항목 하나만 작업한다. 사용자가 다른 걸 지시하면 그걸 따른다.

## 작업 규칙

- SPEC의 "하지 않을 것"은 구현하지 않는다. 필요해 보이면 코드를 쓰지 말고 사용자에게 먼저 제안한다.
- SPEC에 없는 기능, 시스템, 라이브러리, 리팩터링을 임의로 추가하지 않는다.
- PROGRESS의 "결정 기록"에 있는 결정은 뒤집지 않는다. 바꿀 이유가 있으면 먼저 묻는다.
- 게임 규칙(점수, 학점, 주차 진행)은 `src/core/`에 DOM을 모르는 순수 함수로 둔다. `src/ui/`는 core를 호출하고 결과를 그리기만 한다.
- 대사, 수치, 주차 일정 같은 콘텐츠는 `src/data/`의 JSON에 둔다. 코드에 하드코딩하지 않는다.
- 랜덤은 `Math.random`을 직접 쓰지 않고 주입받은 rng 함수를 쓴다. 테스트에서 시드를 고정하기 위해서다.
- 프레임워크(React 등)와 런타임 의존성을 추가하지 않는다. 개발 의존성은 Vite, TypeScript, Vitest만.
- 식별자는 영어, 주석과 UI 텍스트는 한국어.
- 커밋은 Claude Code가 직접 한다. 형식과 단위는 `docs/COMMIT_CONVENTION.md`를 반드시 따른다. push는 사용자가 요청할 때만.

## 작업을 마칠 때

1. `npm run check`(타입 검사 + 테스트)와 `npm run build`가 통과하는지 직접 실행해서 확인한다.
2. `docs/PROGRESS.md`에서 끝낸 항목을 체크한다. 완료 기준을 실제로 확인하지 못했으면 체크하지 말고 무엇이 남았는지 적는다. 브라우저에서 눈으로 확인해야 하는 기준은 사용자에게 확인을 요청한다.
3. "세션 로그"에 한 줄 추가: 날짜, 한 일, 다음 할 일, 막힌 점.
4. 새로 정한 설계 결정이 있으면 "결정 기록"에 이유와 함께 추가한다.
5. `docs/COMMIT_CONVENTION.md`에 맞춰 커밋한다. 변경이 여러 성격이면 커밋을 나눈다.

## 기술 스택

- TypeScript + Vite, 프레임워크 없음 (vanilla DOM)
- 테스트: Vitest
- 배포: GitHub Actions → GitHub Pages (`vite.config.ts`의 `base`는 `/get_an_a_plus/`)
- 대상: 데스크톱 Chrome/Edge, 1280px 이상 너비 기준
- 페이지 하나. 화면 전환은 컨테이너 내용을 바꿔 그리는 방식으로 한다.

## 폴더 구조

```
index.html
src/
  main.ts          진입점, 화면 전환
  style.css
  core/            gameState, weekResolver, gradeCalculator, rng (+ *.test.ts)
  data/
    types.ts       JSON 타입 정의
    loader.ts      import.meta.glob으로 로드 + 검증
    syllabi/       강의계획서
    profiles/      숨겨진 교수 성향
    events/        주차 이벤트
    interviews/    면담 대화
  interview/       InterviewDriver 인터페이스, choiceDriver
  ui/              화면별 render 함수
.github/workflows/ Pages 배포
docs/
  SPEC.md          기획서 (무엇을, 어디까지)
  PROGRESS.md      진행 상황, 결정 기록, 세션 로그
  COMMIT_CONVENTION.md  커밋 규칙
```
