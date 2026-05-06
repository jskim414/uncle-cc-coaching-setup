## Codex 프로젝트별 AGENTS.md 작성 가이드

## 1. AGENTS.md의 역할

AGENTS.md는 Codex가 특정 프로젝트 폴더에서 작업을 시작하기 전에 읽는 프로젝트 작업지침서다.

쉽게 말하면, 매번 프롬프트에 반복해서 설명해야 하는 내용을 프로젝트 폴더 안에 미리 저장해두는 방식이다.

예를 들어 다음과 같은 내용을 저장할 수 있다.

- 이 프로젝트의 목적
- 기술 스택
- 폴더 구조
- 실행 명령어
- 테스트 명령어
- 코딩 스타일
- 파일 수정 원칙
- 작업 결과물 저장 위치
- 금지 사항
- 커밋/배포 전 확인 사항

Codex는 작업할 때 이 파일을 참고하므로, 프로젝트마다 반복 설명을 줄이고 일관된 결과물을 얻을 수 있다.

---

## 2. 핵심 개념: 전역 지침과 프로젝트 지침을 나눠라

Codex의 AGENTS.md는 크게 두 층으로 나눠 생각하면 된다.

| 구분 | 위치 | 역할 |
|---|---|---|
| 전역 지침 | `~/.codex/AGENTS.md` | 모든 프로젝트에 공통 적용할 기본 작업 방식 |
| 프로젝트 지침 | 각 프로젝트 루트의 `AGENTS.md` | 해당 프로젝트에서만 적용할 구체적 작업 방식 |
| 하위 폴더 지침 | 특정 하위 폴더의 `AGENTS.md` 또는 `AGENTS.override.md` | 일부 영역에만 적용할 세부 규칙 |

권장 방식은 다음과 같다.

- `~/.codex/AGENTS.md`에는 나의 기본 작업 취향을 적는다.
- 프로젝트 루트의 `AGENTS.md`에는 해당 프로젝트의 목적과 구조를 적는다.
- 특정 하위 폴더가 별도 규칙을 가져야 할 때만 하위 폴더에 추가 지침을 둔다.

---

## 3. Codex가 AGENTS.md를 읽는 방식

Codex는 작업을 시작할 때 지침 파일을 한 번 읽고 instruction chain을 만든다.

읽는 순서는 대략 다음과 같다.

1. Codex 홈 디렉터리의 전역 지침을 읽는다.
2. 프로젝트 루트에서 현재 작업 디렉터리까지 내려가며 AGENTS.md를 찾는다.
3. 상위 폴더의 지침을 먼저 읽고, 하위 폴더의 지침을 나중에 읽는다.
4. 나중에 읽힌 지침이 더 구체적인 지침으로 작동한다.

즉, 하위 폴더에 가까운 지침일수록 해당 작업에 더 강하게 적용된다.

예시:

```text
my-project/
  AGENTS.md
  app/
    AGENTS.md
  docs/
    AGENTS.md
```

- `my-project/`에서 Codex를 실행하면 루트의 `AGENTS.md`만 읽는다.
- `my-project/app/`에서 실행하면 루트 `AGENTS.md`와 `app/AGENTS.md`를 함께 읽는다.
- `my-project/docs/`에서 실행하면 루트 `AGENTS.md`와 `docs/AGENTS.md`를 함께 읽는다.

---

## 4. 기본 폴더 구조 추천

프로젝트별로 다음 구조를 추천한다.

```text
project-name/
  AGENTS.md
  README.md
  docs/
    project-brief.md
    requirements.md
    implementation-log.md
  src/
  tests/
  package.json
```

문서/지식업무 중심 프로젝트라면 다음 구조가 더 적합하다.

```text
project-name/
  AGENTS.md
  input/
    source.md
  process/
    work-guideline.md
    checklist.md
  output/
    result.md
  archive/
```

사용자가 평소 쓰는 방식으로 정리하면 다음과 같다.

```text
input: 작업 대상
process: 작업 지침
output: 결과물 저장 위치
```

이 구조를 AGENTS.md에 명시하면 Codex가 매번 작업 맥락을 더 정확히 이해한다.

---

## 5. AGENTS.md에 반드시 포함할 항목

프로젝트 루트의 AGENTS.md는 아래 8개 항목으로 작성하면 충분하다.

### 5-1. 프로젝트 개요

이 프로젝트가 무엇을 위한 것인지 3~5줄로 설명한다.

예시:

```md
## Project Overview

이 프로젝트는 클코형 1:1 과외 예약 페이지 MVP를 구현하기 위한 웹 애플리케이션이다.
목표는 신청 → 입금 확인 → 예약 확정 → 사전 안내 → 후기 요청까지의 운영 흐름을 최소 기능으로 구현하는 것이다.
관리자는 예약 슬롯을 수동 등록하고, 신청 내역을 확인하며, 예약 상태를 변경할 수 있어야 한다.
```

### 5-2. 작업 목표

Codex가 이 프로젝트에서 우선해야 할 목표를 적는다.

예시:

```md
## Goals

- 빠르게 운영 가능한 MVP를 우선한다.
- 과도한 아키텍처 설계보다 단순하고 수정 가능한 구조를 우선한다.
- 관리자 기능은 공개 회원가입 없이 allowlist 기반 Google 로그인으로 제한한다.
- 모든 주요 운영 이벤트는 Telegram 알림으로 관리자에게 전달한다.
```

### 5-3. 기술 스택

사용할 기술과 피해야 할 기술을 명시한다.

예시:

```md
## Tech Stack

- Framework: Next.js
- Language: TypeScript
- Styling: Tailwind CSS
- UI: shadcn/ui
- Database: SQLite
- Auth: Google OAuth
- Notification: Telegram Bot
- Deployment: Vercel or Render

Do not introduce new production dependencies without explaining why they are necessary.
```

### 5-4. 폴더 구조

프로젝트의 주요 폴더 역할을 적는다.

예시:

```md
## Project Structure

- `src/app/`: Next.js app routes
- `src/components/`: reusable UI components
- `src/lib/`: shared utility functions
- `src/server/`: server-side logic
- `docs/`: planning documents and implementation notes
- `output/`: generated deliverables
```

### 5-5. 실행/테스트 명령어

Codex가 수정 후 어떤 명령어를 실행해야 하는지 적는다.

예시:

```md
## Commands

- Install dependencies: `pnpm install`
- Run dev server: `pnpm dev`
- Lint: `pnpm lint`
- Type check: `pnpm typecheck`
- Test: `pnpm test`

After changing TypeScript files, run `pnpm lint` and `pnpm typecheck`.
```

### 5-6. 작업 원칙

파일 수정 방식, 문서화 방식, 결과 보고 방식을 적는다.

예시:

```md
## Working Rules

- Before editing, inspect the relevant files and summarize the intended change.
- Prefer small, focused changes over large rewrites.
- Do not delete existing functionality unless explicitly requested.
- When adding a new feature, update the relevant documentation in `docs/`.
- Record implementation notes in `docs/implementation-log.md`.
```

### 5-7. 금지 사항

Codex가 하면 안 되는 일을 명확히 적는다.

예시:

```md
## Do Not

- Do not hardcode API keys or secrets.
- Do not change environment variable names without approval.
- Do not introduce paid external services unless explicitly requested.
- Do not rewrite the entire project when a targeted change is enough.
- Do not remove comments that explain business logic.
```

### 5-8. 결과물 형식

최종 결과를 어디에 어떤 형식으로 저장할지 적는다.

예시:

```md
## Output Expectations

- Save planning documents as Markdown under `docs/`.
- Save generated user-facing copy under `output/`.
- When completing a task, summarize:
  1. What changed
  2. Files modified
  3. Commands run
  4. Remaining risks or TODOs
```

---

## 6. 기본 AGENTS.md 템플릿

아래 템플릿을 프로젝트 루트에 그대로 복사한 뒤, 프로젝트별로 수정하면 된다.

```md
# AGENTS.md

## Project Overview

이 프로젝트의 목적을 3~5줄로 설명한다.
누구를 위한 프로젝트인지, 어떤 문제를 해결하는지, 최종 결과물이 무엇인지 적는다.

## Goals

- 핵심 목표 1
- 핵심 목표 2
- 핵심 목표 3

## Tech Stack

- Framework:
- Language:
- Styling:
- Database:
- Auth:
- Notification:
- Deployment:

## Project Structure

- `input/`: 작업 대상 원본 자료
- `process/`: 작업 지침, 체크리스트, 참고 문서
- `output/`: 최종 결과물
- `docs/`: 프로젝트 설명 및 구현 기록
- `src/`: 소스 코드
- `tests/`: 테스트 코드

## Commands

- Install:
- Dev:
- Lint:
- Type check:
- Test:
- Build:

## Working Rules

- 작업 전 관련 파일을 먼저 확인한다.
- 불필요한 대규모 리팩토링을 하지 않는다.
- 기존 구조와 네이밍을 우선 존중한다.
- 새 기능을 추가하면 관련 문서를 함께 업데이트한다.
- 작업 기록은 `docs/implementation-log.md`에 남긴다.

## Do Not

- API Key, 토큰, 비밀번호를 코드에 직접 넣지 않는다.
- 명시 요청 없이 기술 스택을 바꾸지 않는다.
- 명시 요청 없이 외부 유료 서비스를 추가하지 않는다.
- 작은 수정으로 가능한 일을 전체 재작성하지 않는다.

## Output Expectations

작업 완료 후 다음 형식으로 보고한다.

1. 변경 요약
2. 수정한 파일
3. 실행한 명령어
4. 확인된 문제
5. 다음에 할 일
```

---

## 7. 프로젝트 유형별 AGENTS.md 작성 예시

## 7-1. Next.js 웹앱 프로젝트용

```md
# AGENTS.md

## Project Overview

이 프로젝트는 예약 신청과 관리자 확인 기능을 제공하는 Next.js 기반 MVP다.
사용자는 랜딩페이지에서 서비스를 확인하고 신청서를 제출한다.
관리자는 신청 내역을 확인하고 예약 상태를 변경한다.

## Goals

- 빠르게 운영 가능한 MVP 구현
- 관리자 기능은 단순하고 안전하게 구현
- 외부 SaaS 의존도를 최소화
- 향후 결제, SMS, 알림 확장이 가능한 구조 유지

## Tech Stack

- Next.js App Router
- TypeScript
- Tailwind CSS
- shadcn/ui
- SQLite
- Google OAuth
- Telegram Bot

## Commands

- `pnpm install`
- `pnpm dev`
- `pnpm lint`
- `pnpm typecheck`
- `pnpm build`

## Working Rules

- UI 컴포넌트는 `src/components/`에 둔다.
- 서버 로직은 `src/server/`에 둔다.
- 공통 유틸은 `src/lib/`에 둔다.
- 환경변수는 `.env.example`에 이름만 추가한다.
- 실제 secret 값은 절대 코드나 문서에 기록하지 않는다.
- 기능 구현 후 `pnpm lint`와 `pnpm typecheck`를 실행한다.

## Do Not

- 명시 요청 없이 Prisma, Supabase, Firebase 등을 추가하지 않는다.
- 명시 요청 없이 UI 라이브러리를 교체하지 않는다.
- 인증 우회 로직을 만들지 않는다.
- 관리자 allowlist 정책을 제거하지 않는다.

## Output Expectations

완료 보고에는 변경 파일, 실행 명령어, 남은 TODO를 포함한다.
```

## 7-2. 콘텐츠 제작 프로젝트용

```md
# AGENTS.md

## Project Overview

이 프로젝트는 블로그, 유튜브 스크립트, SNS 콘텐츠를 체계적으로 생산하기 위한 콘텐츠 작업 폴더다.
입력 자료를 바탕으로 작업 지침에 맞춰 초안, 수정본, 최종본을 Markdown으로 생성한다.

## Goals

- 원문의 핵심 논지를 유지한다.
- 불필요한 요약이나 삭제를 하지 않는다.
- 결과물은 복사해 바로 사용할 수 있게 정리한다.
- 제목, 본문, CTA를 분리해 작성한다.

## Project Structure

- `input/`: 원본 자료
- `process/`: 작성 지침, 톤앤매너, 체크리스트
- `output/`: 최종 결과물
- `archive/`: 이전 버전

## Working Rules

- 작업 전 `process/` 폴더의 지침을 먼저 읽는다.
- 원문을 임의로 축약하지 않는다.
- 불확실한 정보는 추정이라고 표시한다.
- 제목 후보는 최소 5개 이상 제시한다.
- 최종 결과물은 Markdown으로 저장한다.

## Do Not

- 원문의 의도를 임의로 바꾸지 않는다.
- 출처 없는 수치를 사실처럼 쓰지 않는다.
- 요청 없이 이모지나 과한 강조를 넣지 않는다.

## Output Expectations

- 최종 파일은 `output/`에 저장한다.
- 파일명은 `YYYY-MM-DD_topic.md` 형식으로 작성한다.
```

## 7-3. IR Deck / 사업계획서 작업 프로젝트용

```md
# AGENTS.md

## Project Overview

이 프로젝트는 초기 스타트업의 IR Deck 또는 사업계획서를 구조화하고 고도화하기 위한 작업 폴더다.
투자자 관점에서 문제, 솔루션, 시장, 경쟁우위, 수익모델, 성장전략, 팀, 투자유치 전략을 정리한다.

## Goals

- 투자자 관점에서 설득 논리를 강화한다.
- 단순 미화보다 사업의 핵심 가정과 검증 논리를 명확히 한다.
- 정량 수치가 부족한 경우에는 필요한 가정과 추가 확인 항목을 표시한다.
- 슬라이드별 핵심 메시지를 한 문장으로 정리한다.

## Project Structure

- `input/`: 기존 IR 자료, 인터뷰 메모, 사업계획서
- `process/`: IR 작성 지침, PSST/BMC/PMF 프레임워크
- `output/`: 수정본, 슬라이드 스토리라인, 발표 대본
- `review/`: 피드백 및 레드팀 리뷰

## Working Rules

- 각 슬라이드는 하나의 핵심 메시지만 가져야 한다.
- 문제 정의는 고객의 경제적 손실이나 비효율과 연결한다.
- 시장 규모는 TAM/SAM/SOM 또는 고객수 × 객단가 방식으로 검토한다.
- 수익모델은 가격, 고객수, 구매빈도, 마진 구조를 함께 본다.
- 불확실한 수치는 `가정`으로 표시한다.
- 투자자 질문 가능성을 별도 섹션으로 정리한다.

## Do Not

- 근거 없는 시장 규모를 확정값처럼 쓰지 않는다.
- 기술 설명만 길게 늘어놓지 않는다.
- 디자인 문구를 위해 사업 논리를 희생하지 않는다.
- 창업자가 제공하지 않은 실적을 임의로 만들지 않는다.

## Output Expectations

작업 완료 시 다음을 제공한다.

1. 슬라이드별 핵심 메시지
2. 수정된 스토리라인
3. 투자자 관점 리스크
4. 추가로 필요한 데이터
5. 다음 작업 제안
```

## 7-4. 리서치 프로젝트용

```md
# AGENTS.md

## Project Overview

이 프로젝트는 특정 시장, 기술, 고객, 경쟁사에 대한 리서치를 수행하고 정리하기 위한 작업 폴더다.
리서치 결과는 의사결정 가능한 형태의 Markdown 문서로 정리한다.

## Goals

- 출처 기반으로 정리한다.
- 사실과 해석을 분리한다.
- 의사결정에 필요한 시사점을 도출한다.
- 불확실하거나 검증이 필요한 내용은 별도로 표시한다.

## Project Structure

- `input/`: 조사 대상, 질문, 기존 자료
- `sources/`: 원문 링크, PDF, 캡처 자료
- `output/`: 리서치 요약, 인사이트 문서
- `memo/`: 중간 메모

## Working Rules

- 최신성이 중요한 정보는 반드시 최신 자료 기준으로 확인한다.
- 출처가 불명확한 내용은 사용하지 않는다.
- 수치는 단위와 기준 연도를 함께 표시한다.
- 최종 문서는 `Fact`, `Interpretation`, `Implication`으로 구분한다.

## Do Not

- 출처 없는 주장을 사실처럼 쓰지 않는다.
- 오래된 자료를 최신 현황처럼 쓰지 않는다.
- 단일 출처만 보고 결론을 확정하지 않는다.

## Output Expectations

최종 결과물은 다음 구조로 작성한다.

1. 핵심 요약
2. 주요 사실
3. 분석 및 해석
4. 사업적 시사점
5. 남은 질문
6. 참고 출처
```

---

## 8. AGENTS.override.md는 언제 쓰나

`AGENTS.override.md`는 같은 위치의 `AGENTS.md`보다 우선 적용되는 임시 또는 특수 지침이다.

다음 경우에만 쓰는 것이 좋다.

- 특정 하위 서비스만 완전히 다른 테스트 명령어를 쓸 때
- 임시로 기존 지침을 무시해야 할 때
- 보안상 절대 지켜야 할 예외 규칙이 있을 때
- 같은 프로젝트 안에서 완전히 다른 작업 방식이 필요한 폴더가 있을 때

예시:

```text
project/
  AGENTS.md
  services/
    payment/
      AGENTS.override.md
```

`services/payment/AGENTS.override.md` 예시:

```md
# AGENTS.override.md

## Payment Service Rules

- 결제 관련 파일은 수정 전 반드시 영향 범위를 먼저 설명한다.
- 결제 금액, 환불 정책, 웹훅 검증 로직은 임의 변경하지 않는다.
- 테스트는 `pnpm test:payment`를 사용한다.
- 결제 API secret은 코드, 로그, 문서에 절대 노출하지 않는다.
```

---

## 9. AGENTS.md 작성 시 좋은 원칙

## 9-1. 너무 길게 쓰지 않는다

AGENTS.md는 백과사전이 아니라 작업지침서다.

좋은 기준:

- 프로젝트 루트 AGENTS.md: 100~200줄 이내
- 하위 폴더 AGENTS.md: 30~80줄 이내
- 긴 설명은 `docs/`에 별도 문서로 분리

## 9-2. 추상적 표현보다 행동 규칙으로 쓴다

나쁜 예:

```md
- 좋은 코드를 작성한다.
- 사용자를 고려한다.
- 깔끔하게 정리한다.
```

좋은 예:

```md
- UI 변경 시 모바일 화면 기준으로 먼저 확인한다.
- 새 컴포넌트는 `src/components/`에 만들고, 페이지 파일에는 배치 로직만 둔다.
- 작업 완료 후 수정 파일과 테스트 결과를 요약한다.
```

## 9-3. 명령어를 구체적으로 쓴다

나쁜 예:

```md
- 테스트를 실행한다.
```

좋은 예:

```md
- TypeScript 파일 수정 후 `pnpm lint`와 `pnpm typecheck`를 실행한다.
- API 라우트 수정 후 `pnpm test:api`를 실행한다.
```

## 9-4. 금지 사항을 명확히 쓴다

Codex는 명시된 제약을 잘 따른다. 따라서 절대 하지 말아야 할 것은 별도 섹션으로 둔다.

예시:

```md
## Do Not

- `.env` 파일의 실제 값을 출력하지 않는다.
- 기존 DB schema를 삭제하지 않는다.
- 명시 요청 없이 외부 결제 모듈을 추가하지 않는다.
```

## 9-5. 결과 보고 양식을 고정한다

Codex의 작업 완료 보고가 매번 달라지는 것을 막기 위해 보고 양식을 고정한다.

예시:

```md
## Completion Report Format

작업 완료 후 다음 형식으로 보고한다.

1. 변경 요약
2. 수정 파일
3. 실행 명령어
4. 테스트 결과
5. 남은 리스크
6. 다음 작업 제안
```

---

## 10. 사용자 작업 방식에 맞춘 추천 운영법

사용자의 작업 스타일을 기준으로 하면, AGENTS.md는 다음처럼 운용하는 것이 가장 효율적이다.

## 10-1. 프로젝트 폴더마다 최소 3개 문서를 둔다

```text
project-name/
  AGENTS.md
  input/
  process/
    work-guideline.md
  output/
```

- `AGENTS.md`: Codex가 항상 따라야 할 기본 운영 규칙
- `input/`: 이번 작업의 원본 자료
- `process/work-guideline.md`: 구체적 작업 지침
- `output/`: 최종 결과물 저장 위치

## 10-2. 프롬프트는 짧게 유지한다

예시:

```text
input/source.md를 읽고,
process/work-guideline.md의 기준에 따라,
output/result.md로 정리해줘.

작업 후 수정 요약과 추가 확인 필요사항을 알려줘.
```

이 방식이 가능한 이유는 반복 지침을 AGENTS.md에 넣어두었기 때문이다.

## 10-3. 작업이 반복될수록 AGENTS.md를 개선한다

Codex 결과물이 마음에 들지 않았던 이유를 다음 작업 전에 AGENTS.md에 반영한다.

예시:

- 결과물이 너무 길다 → `Output should be concise and structured.` 추가
- 원문을 임의로 요약한다 → `Do not summarize or delete source content unless requested.` 추가
- 파일명을 제멋대로 만든다 → `Use YYYY-MM-DD_topic.md for output filenames.` 추가
- 테스트를 안 돌린다 → `Run pnpm lint and pnpm typecheck after code changes.` 추가

이렇게 하면 프로젝트 폴더 자체가 점점 좋은 작업 시스템으로 축적된다.

---

## 11. 검증 명령어

AGENTS.md가 제대로 로드되는지 확인하려면 프로젝트 루트에서 다음처럼 실행한다.

```bash
codex --ask-for-approval never "Summarize the current instructions."
```

특정 하위 폴더 기준으로 어떤 지침이 적용되는지 확인하려면 다음처럼 실행한다.

```bash
codex --cd subdir --ask-for-approval never "Show which instruction files are active."
```

Codex가 다른 지침을 읽는 것 같다면 다음을 확인한다.

- 현재 작업 디렉터리가 맞는가?
- Git 루트가 예상한 위치인가?
- 상위 폴더에 `AGENTS.override.md`가 있는가?
- 파일이 비어 있지는 않은가?
- Codex 세션을 새로 시작했는가?

---

## 12. 추천 결론

프로젝트별 AGENTS.md는 복잡하게 만들 필요가 없다.

가장 실용적인 구조는 다음이다.

```text
1. 이 프로젝트가 무엇인지
2. 어떤 결과물을 만들 것인지
3. 어떤 기술/도구를 쓸 것인지
4. 어떤 폴더에 무엇을 저장할 것인지
5. 어떤 명령어를 실행할 것인지
6. 절대 하지 말아야 할 것은 무엇인지
7. 작업 완료 후 어떻게 보고할 것인지
```

특히 개발 프로젝트가 아니라 지식업무 프로젝트라면 아래 구조만 잘 잡아도 충분하다.

```text
input: 원본 자료
process: 작업 지침
output: 결과물
AGENTS.md: 반복 적용할 작업 원칙
```

핵심은 AGENTS.md를 한 번에 완벽하게 쓰는 것이 아니라, 프로젝트를 반복하면서 계속 업데이트하는 것이다.

AGENTS.md는 단순 사용설명서가 아니라, 프로젝트별로 Codex를 길들이는 누적 작업 시스템이다.

