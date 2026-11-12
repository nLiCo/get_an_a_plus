# PROGRESS

마감: 2026-12-09 무렵 (확인 필요)
현재 마일스톤: M0

## 마일스톤

### M0 세팅 (~11/14)

- [ ] Vite vanilla-ts 프로젝트 구성 (기존 docs/, CLAUDE.md 유지), Vitest 추가
- [ ] npm 스크립트: `dev`, `build`, `test`, `check`(tsc --noEmit + vitest run)
- [ ] Claude Code hook: `.claude/settings.json` + `.claude/hooks/*.mjs` (PostToolUse → tsc, Stop → npm run check), SPEC 11장
  - 완료 기준: 일부러 타입 에러를 남긴 채 턴을 끝내려 하면 Stop hook이 막고 Claude가 고치러 간다
- [ ] git hook: simple-git-hooks로 pre-commit(`npm run check`), commit-msg(commitlint, `balance` type 허용)
  - 완료 기준: `fix 버그` 같은 형식 위반 메시지 커밋이 거부되고, 올바른 메시지는 통과한다
- [ ] GitHub Actions 워크플로: check + build 통과 시에만 Pages 배포 (`base: '/get_an_a_plus/'`)
  - 완료 기준: main에 push하면 Pages 링크에서 "Hello" 수준의 페이지가 뜨고, 테스트를 일부러 깨면 배포 단계가 실행되지 않는다 (사용자가 저장소 Settings → Pages에서 Source를 GitHub Actions로 설정해야 함)
- [ ] `data/types.ts`, `data/loader.ts`, 샘플 계획서 3개 JSON
  - 완료 기준: loader 테스트 통과, 잘못된 JSON(필드 누락, 없는 id 참조)을 넣으면 파일명과 필드명이 담긴 에러가 난다

### M1 핵심 루프 (~11/19), 스타일 없이 텍스트와 버튼만

- [ ] `rng`, `gameState`, 1~15주 진행
- [ ] lecture 행동 4종
- [ ] exam 이벤트
- [ ] `gradeCalculator` + 테스트 (경계값: 컷 점수, trust ±100)
- [ ] 결과 화면 (텍스트)
- [ ] `?seed=` 쿼리 지원, Playwright 설치, E2E 한 판 테스트 (`npm run e2e`), CI에 E2E 추가
  - 완료 기준: 계획서 선택 → 15주 → 학점 표시까지 버튼만으로 끊김 없이 한 판 끝나고, 이 흐름이 E2E 테스트로 자동 통과한다

### M2 시스템 완성 (~11/26)

- [ ] quiz, assignment 이벤트
- [ ] 숨겨진 프로필 랜덤 배정, 단서 노출, 단서 메모 UI
- [ ] 면담: `InterviewDriver` + `choiceDriver`, requires 조건과 비활성 표시
- [ ] 체력 0 강제 결석
  - 완료 기준: 같은 계획서로 두 판 했을 때 프로필이 달라질 수 있고, 단서만 보고 성향을 추리할 수 있다

### M3 콘텐츠와 완성도 (~12/3)

- [ ] 계획서 3종 콘텐츠 (각 프로필 2~3개, 면담 2회분, 15주 일정)
- [ ] UI 정리: 계획서 카드, 체력바, 신뢰도 게이지, 결과 화면 진짜 성향 공개
- [ ] 밸런스: 계획서마다 A+ 가능, 대충 하면 B 이하 (사용자 플레이테스트)
- [ ] 배포 링크에서 Chrome, Edge로 한 판씩 확인
- [ ] README: 플레이 링크, 스크린샷, 게임 설명, 로컬 실행 방법

### M4 마무리 (~12/9)

- [ ] 버그 수정
- [ ] 시연 영상
- [ ] (선택, 사용자 승인 시에만) LLM 면담, SPEC 10장

## 결정 기록

- 2026-10-28: 주제 확정. 교수에게 A+를 받아내는 게임. 이유: 교수님이 직접 해보고 싶어질 결과물이고, 과제 맥락과 맞물림.
- 2026-10-28: 강의계획서 3장 중 선택 후 15주차로 진행하는 구조.
- 2026-10-28: LLM은 필수 아님. 선택지 면담으로 전체를 먼저 완성하고, 시간이 남으면 면담만 LLM으로 교체. 이유: AI 사용은 과제 요구사항이 아니고, 넣으면 일정 리스크가 큼.
- 2026-10-28: 교수 NPC와 과목은 가상. 실제 교수님 이름이나 말투를 쓰지 않음.
- 2026-10-28: 오픈소스 라이브러리 사용이나 오픈소스화는 의무 아님. 본인 프로젝트를 만든 경험이 중요.
- 2026-11-12: Claude Code 개발용으로 CLAUDE.md / docs/SPEC.md / docs/PROGRESS.md 구성.
- 2026-11-12: Unity 대신 웹(TypeScript + Vite, 프레임워크 없음)으로 개발, GitHub Pages 배포. 이유: 텍스트와 버튼 중심이라 엔진 기능이 필요 없고, 교수님이 설치 없이 링크로 바로 플레이할 수 있으며, Claude Code가 에디터 수작업 없이 전부 코드로 다룰 수 있음. LLM은 선택 사항이라 웹의 불리함은 감수.

- 2026-11-12: 커밋은 Claude Code가 Conventional Commits 기반 규칙(docs/COMMIT_CONVENTION.md)으로 직접 한다. 이유: AI를 최대한 활용하는 게 개인 목표이고, 나중에 히스토리를 보며 커밋 방법을 익히려고. push는 사용자 요청 시에만.
- 2026-11-12: 검증을 기계로 강제한다 (SPEC 11장). Claude Code hook, git hook + commitlint, CI 게이트 배포, Playwright E2E. 이유: CLAUDE.md 지시는 권고라 무시될 수 있지만 hook과 CI는 반드시 실행됨. 리뷰 서브에이전트는 과잉 설계 위험이 있어 보류.

## 세션 로그

형식: `- YYYY-MM-DD: 한 일 / 다음 할 일 / 막힌 점`

- 2026-11-12: 기획 문서 3종 작성 후 Unity → 웹으로 전환, .gitignore를 Node용으로 교체, 커밋 컨벤션 문서 추가, 검증 자동화 계획 추가 / M0 첫 항목 (Vite 프로젝트 구성) / 없음
