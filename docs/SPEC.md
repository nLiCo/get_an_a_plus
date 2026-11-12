# SPEC: get_an_a_plus

> "(초안)" 표시가 붙은 수치와 규칙은 밸런스 조정 대상이다. 조정하면 이 문서를 고치고 PROGRESS의 결정 기록에 남긴다.

## 1. 한 줄 컨셉

강의계획서 3장 중 하나를 골라 15주 한 학기를 보내며, 숨겨진 평가 성향을 가진 가상의 교수에게 A+를 받아내는 웹 턴제 게임.

## 2. 프로젝트 조건

- 오픈소스소프트웨어 수업 개인 과제. 혼자 개발한다.
- 마감: 2026-12-09 무렵 (2026-10-28 기준 6주 뒤, 정확한 날짜는 사용자 확인 필요).
- 담당 교수님은 눈에 보이는 결과물을 좋아한다. 링크 하나로 바로 끝까지 해볼 수 있는 배포본이 가장 중요하다.
- 제출물: 제목, 짧은 설명, GitHub URL. README 맨 위에 플레이 링크(GitHub Pages)를 둔다.

## 3. 우선순위 원칙

1. 처음부터 끝까지 한 판이 돌아가는 것 > 기능 개수 > 연출.
2. 각 마일스톤이 끝날 때마다 배포 링크에서 실행 가능한 상태를 유지한다.
3. 막히면 기능을 줄인다. 일정을 미루지 않는다.

## 4. 범위

### 필수

- 강의계획서 3장 중 선택
- 15주차 진행, 주차별 이벤트 (일반 수업, 퀴즈, 과제, 면담, 시험)
- 플레이어 체력과 기록 (출석, 과제, 퀴즈, 시험, 질문, 신뢰도)
- 숨겨진 교수 성향 랜덤 배정과 단서 시스템
- 선택지 방식 면담
- 최종 학점 계산과 결과 화면 (진짜 성향 공개 포함)
- GitHub Pages 배포, README

### 하지 않을 것

- 로컬 LLM 면담: M3 완료 후 사용자가 명시적으로 승인하기 전에는 착수하지 않는다. 지금은 `InterviewDriver` 인터페이스로 자리만 분리해둔다.
- 세이브/로드 (한 판 15~20분 목표)
- 직접 그린 아트, 픽셀아트, 애니메이션. CSS 패널, 도형, 텍스트로 만든다. 간단한 CSS 전환 효과 정도는 M3에서 허용.
- 사운드
- 설정 메뉴
- 모바일 전용 레이아웃, 앱 패키징
- 백엔드 서버, 로그인, 랭킹
- UI 프레임워크(React, Vue 등)와 게임 엔진 라이브러리
- 실제 교수님 이름, 말투, 과목을 흉내 낸 NPC. 교수와 과목은 모두 가상이다.

## 5. 게임 흐름

```
타이틀 → 강의계획서 선택(3장) → 1~15주차 루프 → 결과 화면 → 타이틀
```

주차 루프 한 번:
1. 이번 주 이벤트 표시 (계획서의 schedule에서 결정)
2. 플레이어가 선택
3. 결과 적용 (체력, 기록, 신뢰도, 단서)
4. 결과 텍스트 표시 → 다음 주

주차 일정은 계획서마다 JSON으로 다르게 정의한다. 기본 틀(초안): 1주 OT, 8주 중간고사, 15주 기말고사, 면담 2회(예: 5주, 12주), 나머지는 일반 수업, 퀴즈, 과제.

## 6. 시스템

### 6.1 플레이어 상태

| 항목 | 범위 | 설명 |
|---|---|---|
| stamina | 0~100, 시작 100 | 행동마다 소모, 휴식으로 회복 |
| attendance | 출석한 주 수 | |
| assignmentScores | 0~100 배열 | |
| quizScores | 0~100 배열 | |
| midterm, final | 0~100 | |
| questions | 질문 횟수 | |
| trust | -100~100, 시작 0 | 면담 선택으로 변화 |
| clues | 문자열 배열 | 발견한 성향 단서 |

체력이 0이 되면 다음 주는 강제 결석 처리하고 체력 30으로 회복한다 (초안).

### 6.2 교수 성향 (숨김)

- 평가 항목은 4개: attendance(출석), assignment(과제), exam(시험), passion(열정: 질문, 면담 태도).
- 계획서에는 공식 비율 `statedWeights`가 적혀 있다.
- 실제 채점은 숨겨진 프로필의 `trueWeights`로 한다. 공식 비율과 다를 수 있다.
- 계획서마다 프로필 후보 2~3개를 두고, 게임 시작 시 하나를 랜덤으로 고른다. 같은 계획서도 판마다 교수가 다를 수 있다.
- 각 프로필은 단서 문장을 가진다. 이벤트 결과 텍스트에 섞여 나오고, 나오면 clues에 저장된다.

### 6.3 주차 이벤트 유형

**lecture (일반 수업)**: 행동 하나 선택 (초안 수치)
- 수업 듣기: 출석 +1, 체력 -10
- 질문하기: 출석 +1, 질문 +1, 체력 -15
- 과제 미리 하기: 출석 +1, 다음 과제 점수 +10, 체력 -15
- 자체 휴강: 체력 +25, 출석 없음

**quiz**: 점수 = 50 + 최근 3주 출석 수 × 10 + (체력 ≥ 50 이면 +10) + 랜덤(-10~+10), 0~100 클램프 (초안)

**assignment**: 선택
- 성실하게: 체력 -30, 점수 80~100
- 적당히: 체력 -15, 점수 55~75
- 안 냄: 체력 0, 점수 0

**interview (면담)**: 3~5턴. 턴마다 교수 대사 + 선택지 3개.
- 선택지는 `tag`(attendance/assignment/exam/passion/none)와 `baseTrust`를 가진다.
- 선택지 tag가 현재 교수 trueWeights의 최댓값 항목과 같으면 `bonusTrust`를 추가로 받는다.
- 증거 선택지: `requires` 조건(예: 출석 10회 이상)을 만족해야 고를 수 있다. 불만족이면 비활성 표시.
- 면담 중 교수 반응 텍스트로 단서가 드러날 수 있다.

**exam (중간/기말)**: 점수 = 40 + 출석률 × 30 + 퀴즈 평균 × 0.2 + (체력 ≥ 50 이면 +10) + 랜덤(-5~+5), 0~100 클램프 (초안)

### 6.4 최종 학점 (초안)

항목별 정규화 (0~1):
- attendance = 출석 수 / 출석 가능한 주 수
- assignment = 과제 평균 / 100
- exam = (중간 + 기말) / 200
- passion = min(1, 질문 수 / 5)

```
score = (Σ trueWeights[k] × normalized[k]) × 100 + trust / 10
```

trust 보정은 최대 ±10. 학점 컷: A+ ≥ 95, A ≥ 90, B+ ≥ 85, B ≥ 80, C+ ≥ 75, C ≥ 70, D ≥ 60, 그 외 F.

밸런스 목표: 각 계획서에서 성향을 잘 추리하고 맞춰 플레이하면 A+가 가능하고, 성향을 무시하고 대충 하면 B 이하.

## 7. 데이터 구조

JSON 파일은 `src/data/` 아래에 두고 `import.meta.glob`으로 한꺼번에 불러온다. 타입은 `src/data/types.ts`에 정의하고, `loader.ts`가 로드 시점에 필수 필드와 참조(id) 무결성을 검사한다. 잘못된 데이터면 어떤 파일의 어떤 필드인지 에러를 낸다.

### 강의계획서 `src/data/syllabi/*.json`

```json
{
  "id": "oss_intro",
  "courseName": "오픈소스소프트웨어개론",
  "professorName": "강민철",
  "intro": "출석은 기본입니다. 코드는 거짓말을 하지 않습니다.",
  "statedWeights": { "attendance": 0.1, "assignment": 0.4, "exam": 0.4, "passion": 0.1 },
  "profileIds": ["oss_strict_attendance", "oss_passion_lover"],
  "schedule": [
    { "week": 1, "type": "lecture", "eventId": "ot_default" },
    { "week": 5, "type": "interview", "eventId": "oss_interview_1" },
    { "week": 8, "type": "exam", "eventId": "midterm_default" },
    { "week": 15, "type": "exam", "eventId": "final_default" }
  ]
}
```

schedule은 1~15주를 모두 채운다.

### 교수 성향 프로필 `src/data/profiles/*.json`

```json
{
  "id": "oss_strict_attendance",
  "label": "출석 집착형",
  "trueWeights": { "attendance": 0.4, "assignment": 0.2, "exam": 0.3, "passion": 0.1 },
  "clues": [
    "교수님이 출석부를 두 번이나 확인하신다.",
    "\"지난주 빠진 사람, 이유서 내세요.\""
  ]
}
```

### 주차 이벤트 `src/data/events/*.json`

```json
{
  "id": "ot_default",
  "type": "lecture",
  "title": "1주차: 오리엔테이션",
  "text": "교수님이 강의계획서를 읽어주신다.",
  "clueChance": 0.5
}
```

lecture 행동 4종과 assignment 선택 3종의 효과는 type별 공통 규칙(6.3)으로 코드에서 처리하고, 이벤트 JSON은 텍스트와 단서 확률만 가진다.

### 면담 `src/data/interviews/*.json`

```json
{
  "id": "oss_interview_1",
  "turns": [
    {
      "line": "무슨 일로 찾아왔나?",
      "choices": [
        { "text": "지금까지 한 번도 결석 안 했습니다.", "tag": "attendance", "baseTrust": 5, "bonusTrust": 10,
          "requires": { "attendanceMin": 4 } },
        { "text": "과제 피드백을 받고 싶어서요.", "tag": "assignment", "baseTrust": 5, "bonusTrust": 10 },
        { "text": "그냥... 인사드리러 왔습니다.", "tag": "none", "baseTrust": -5, "bonusTrust": 0 }
      ]
    }
  ]
}
```

`requires`는 생략 가능하다.

## 8. 코드 구조

- `core/gameState.ts`: 6.1의 상태 타입과 초기 상태 생성. 상태는 불변으로 다루고 새 객체를 반환한다.
- `core/weekResolver.ts`: (상태, 이벤트, 선택, rng) → { 새 상태, 결과 텍스트 }.
- `core/gradeCalculator.ts`: (상태, trueWeights) → { 점수, 학점, 항목별 점수 }.
- `core/rng.ts`: 시드 고정 가능한 난수 함수 (예: mulberry32).
- `data/loader.ts`: JSON 로드와 검증.
- `interview/`: `InterviewDriver` 인터페이스(한 턴 진행). 지금은 `choiceDriver`만 구현.
- `ui/`: 화면별 render 함수 (title, syllabusSelect, week, interview, result, clueNote). core를 호출하고 결과만 그린다.
- `main.ts`: 현재 화면 상태를 들고 화면 전환.

테스트는 core와 loader에 집중한다. UI는 테스트하지 않고 브라우저에서 확인한다.

## 9. 화면

- 타이틀: 게임 제목, 시작 버튼
- 계획서 선택: 카드 3장 (과목명, 교수명, 평가 비율, 교수 한마디), 카드 클릭으로 선택
- 주차 화면: 상단 "n주차 / 15"와 체력바, 오른쪽 기록 요약, 가운데 이벤트 텍스트와 선택 버튼, 단서 메모 버튼
- 면담 화면: 교수 대사, 선택지 3개 (조건 미달은 비활성 + 이유 표시), 신뢰도 게이지
- 단서 메모: 지금까지 얻은 단서 목록 팝업
- 결과 화면: 학점 크게, 항목별 점수, 교수의 진짜 성향(label, trueWeights) 공개, 다시 하기 버튼

## 10. 확장: LLM 면담 (승인 전 착수 금지)

M3 완료 후 사용자가 승인하면 진행한다. 참고용 메모:

- `InterviewDriver`의 새 구현체로 `llmDriver`를 추가한다.
- 모델은 교수 대사만 생성하고, 판정은 JSON `{ "tag": "...", "trustDelta": n }`으로 받는다. trustDelta는 -10~+10으로 클램프한다. 학점은 항상 코드가 계산한다.
- 응답이 형식에 안 맞거나 실패하거나, 브라우저가 지원하지 않으면 선택지 면담으로 폴백한다.
- 실행 방식 후보: 브라우저 내 추론(WebLLM 등, WebGPU 필요, 첫 실행 시 모델 다운로드) 또는 사용자 PC의 로컬 서버(Ollama 등) 연결. 어느 쪽이든 어떤 한국어 소형 모델이 실제로 돌아가는지 먼저 검증한다.
- 후보 모델: Kanana-2 1.3B/3B, HyperCLOVA X SEED 1.5B, Qwen3.5 2B. 배포 시 각 라이선스 고지 조건을 지킨다.
