# 커밋 컨벤션

이 프로젝트의 커밋은 Claude Code가 작성한다. 사용자가 나중에 히스토리를 보고 커밋 방법을 배울 수 있도록 아래 규칙을 엄격하게 지킨다. [Conventional Commits 1.0.0](https://www.conventionalcommits.org/ko/v1.0.0/)을 따른다.

## 형식

```
<type>(<scope>): <요약>

<본문: 무엇을, 왜>

<꼬리말>
```

- 요약: 한국어, 50자 안팎, 마침표 없음, "~한다"가 아니라 "~ 추가", "~ 수정"처럼 명사형으로 끝낸다.
- 본문: 선택. 요약만으로 이유가 안 보일 때 쓴다. "어떻게"보다 "왜"를 쓴다. 한 줄 72자 안팎에서 줄바꿈.
- 꼬리말: 관련 마일스톤(`Refs: M1`), 호환이 깨지는 변경(`BREAKING CHANGE: ...`), AI 공동 작성 표시(`Co-Authored-By: ...`).

## type

| type | 언제 | 예 |
|---|---|---|
| feat | 플레이어가 체감하는 기능이나 콘텐츠 추가 | `feat(interview): 증거 선택지 조건 검사 추가` |
| fix | 버그 수정 | `fix(core): 체력이 음수로 내려가는 문제 수정` |
| refactor | 동작은 그대로, 구조만 변경 | `refactor(ui): 화면 render 함수 공통 부분 분리` |
| test | 테스트만 추가, 수정 | `test(core): 학점 컷 경계값 테스트 추가` |
| docs | 문서만 변경 | `docs: SPEC 면담 규칙 보완` |
| style | 코드 포맷, 세미콜론 등 의미 없는 변경 (CSS 디자인 변경은 feat) | `style: 들여쓰기 정리` |
| build | 빌드 설정, 의존성 | `build: Vitest 추가` |
| ci | GitHub Actions | `ci: Pages 배포 워크플로 추가` |
| chore | 그 외 잡일 (.gitignore 등) | `chore: .gitignore 정리` |
| balance | 게임 수치 조정 (이 프로젝트 전용 type) | `balance(data): 퀴즈 기본 점수 50→45` |

## scope

`core`, `data`, `ui`, `interview`, `deploy`, `docs` 중 하나. 여러 곳에 걸치거나 애매하면 생략한다.

## 커밋 단위

- 커밋 하나에는 논리적인 변경 하나만 담는다. "기능 추가 + 상관없는 버그 수정"은 두 커밋으로 나눈다.
- PROGRESS 체크박스 하나는 커밋 하나 이상. 그 항목을 끝낸 커밋에 `docs/PROGRESS.md` 갱신을 함께 담는다.
- 커밋 전에 `npm run check`와 `npm run build`가 통과해야 한다 (M0 세팅 전에는 해당 없음). 실패하는 상태로 커밋하지 않는다.
- `git add .` 대신 바꾼 파일을 명시해서 스테이징하고, 커밋 전에 `git diff --staged`로 의도하지 않은 파일이 없는지 확인한다.
- 이미 커밋한 것을 amend, rebase, force push로 고치지 않는다. 잘못됐으면 새 커밋으로 고친다.

## 브랜치와 push

- 혼자 하는 프로젝트라 `main`에 바로 커밋한다.
- push는 사용자가 요청할 때만 한다. (push하면 Pages 배포가 돌아간다.)

## 예시

```
feat(core): 일반 수업 행동 4종 처리 추가

수업 듣기, 질문하기, 과제 미리 하기, 자체 휴강의 체력과 기록 변화를
SPEC 6.3 초안 수치대로 구현했다. 수치는 data가 아니라 공통 규칙이라
weekResolver 안에 상수로 두었다.

Refs: M1
Co-Authored-By: Claude <noreply@anthropic.com>
```
