# Claude Code CLAUDE.md 작성 가이드

> 공식 문서([docs.anthropic.com/en/docs/claude-code/memory](https://docs.anthropic.com/en/docs/claude-code/memory)) 기반으로 정리한 프로젝트별 CLAUDE.md 작성 가이드입니다.

---

## 목차

1. [CLAUDE.md란?](#1-claudemd란)
2. [파일 위치 및 범위](#2-파일-위치-및-범위)
3. [로딩 메커니즘](#3-로딩-메커니즘)
4. [효과적인 작성법](#4-효과적인-작성법)
5. [프로젝트 유형별 템플릿](#5-프로젝트-유형별-템플릿)
6. [고급 기능](#6-고급-기능)
7. [운영 및 유지보수](#7-운영-및-유지보수)
8. [자주 하는 실수 & 디버깅](#8-자주-하는-실수--디버깅)

---

## 1. CLAUDE.md란?

CLAUDE.md는 Claude Code가 **세션 시작 시 자동으로 읽는 프로젝트 전용 지시사항 파일**입니다. Claude Code는 매 대화를 빈 컨텍스트 윈도우로 시작하기 때문에, CLAUDE.md에 프로젝트 규칙을 작성해두면 세션이 바뀌어도 동일한 컨텍스트를 유지할 수 있습니다.

### README.md vs CLAUDE.md

| 항목 | README.md | CLAUDE.md |
|------|-----------|-----------|
| 독자 | 사람 | Claude (AI) |
| 목적 | 프로젝트 소개 및 사용법 | Claude가 따라야 할 코딩 규칙과 워크플로우 |
| 내용 | 마케팅적, 개요적 | 구체적, 실행 가능한 지시사항 |

### CLAUDE.md vs Auto Memory

| 항목 | CLAUDE.md | Auto Memory |
|------|-----------|-------------|
| 작성 주체 | 개발자 (직접 작성) | Claude (자동 기록) |
| 내용 | 규칙, 지시사항 | 학습한 패턴, 선호도 |
| 범위 | 프로젝트·사용자·조직 | 작업 디렉토리별 |
| 로딩 | 매 세션 전체 로드 | 매 세션 (최대 200줄 또는 25KB) |
| 활용 | 코딩 표준, 워크플로우, 아키텍처 | 빌드 명령, 디버깅 인사이트, 발견된 선호도 |

---

## 2. 파일 위치 및 범위

Claude Code는 **위치별로 다른 범위**의 CLAUDE.md를 지원합니다. 더 구체적인 위치가 더 넓은 범위보다 우선 적용됩니다.

### 위치 계층 구조

```
/Library/Application Support/ClaudeCode/CLAUDE.md   ← 조직 전체 (관리형 정책)
~/.claude/CLAUDE.md                                  ← 개인 전체 (모든 프로젝트)
~/.claude/rules/*.md                                 ← 개인 전체 규칙 파일
{프로젝트 루트}/CLAUDE.md                             ← 프로젝트 공용 (팀 공유)
{프로젝트 루트}/.claude/CLAUDE.md                    ← 프로젝트 공용 (대체 위치)
{프로젝트 루트}/.claude/rules/*.md                   ← 프로젝트 규칙 파일
{프로젝트 루트}/CLAUDE.local.md                      ← 개인용 (Git 제외)
{하위 디렉토리}/CLAUDE.md                            ← 특정 디렉토리 전용
```

### 위치별 세부 설명

#### 조직 전체 (Managed Policy)
- **위치**: macOS `/Library/Application Support/ClaudeCode/CLAUDE.md`, Linux `/etc/claude-code/CLAUDE.md`
- **목적**: IT/DevOps가 관리하는 회사 전체 지침
- **특징**: 개인 설정으로 제외 불가, 항상 적용됨
- **용도**: 회사 코딩 표준, 보안 정책, 컴플라이언스 요구사항

#### 개인 전체 (User Instructions)
- **위치**: `~/.claude/CLAUDE.md`
- **목적**: 모든 프로젝트에 적용할 개인 선호 사항
- **용도**: 코드 스타일 선호도, 개인 툴링 단축키

#### 프로젝트 공용 (Project Instructions)
- **위치**: `./CLAUDE.md` 또는 `./.claude/CLAUDE.md`
- **목적**: 팀 전체가 공유하는 프로젝트 지침
- **특징**: Git으로 팀원과 공유
- **용도**: 프로젝트 아키텍처, 코딩 표준, 공통 워크플로우

#### 개인용 로컬 (Local Instructions)
- **위치**: `./CLAUDE.local.md`
- **목적**: 개인적인 프로젝트별 설정 (Git 제외)
- **특징**: `.gitignore`에 반드시 추가
- **용도**: 개인 샌드박스 URL, 선호하는 테스트 데이터

#### 하위 디렉토리 (Subdirectory)
- **위치**: 각 하위 디렉토리의 `CLAUDE.md`
- **특징**: 해당 디렉토리의 파일 작업 시에만 지연 로드
- **용도**: 모노레포 내 서비스별 특화 규칙

---

## 3. 로딩 메커니즘

### 로딩 순서

Claude Code는 현재 작업 디렉토리에서 시작해 루트(`/`)까지 거슬러 올라가면서 `CLAUDE.md`와 `CLAUDE.local.md` 파일을 찾아 **모두 병합**하여 로드합니다.

```
실행 위치: /project/src/api/

로드 순서 (파일시스템 루트 → 작업 디렉토리):
1. /CLAUDE.md (있으면)
2. /project/CLAUDE.md
3. /project/CLAUDE.local.md
4. /project/src/CLAUDE.md
5. /project/src/CLAUDE.local.md
6. /project/src/api/CLAUDE.md        ← 가장 마지막 = 최고 우선순위
7. /project/src/api/CLAUDE.local.md
```

하위 디렉토리의 CLAUDE.md는 해당 디렉토리의 파일을 읽을 때 **지연 로드(lazy load)** 됩니다.

### `/compact` 이후 동작

- 프로젝트 루트 CLAUDE.md는 컴팩션 후 **자동으로 재주입**됩니다.
- 하위 디렉토리의 CLAUDE.md는 자동 재주입되지 않으며, 해당 디렉토리 파일 접근 시 다시 로드됩니다.

---

## 4. 효과적인 작성법

### 핵심 원칙

**CLAUDE.md는 "매번 다시 설명하게 되는 것"을 적어두는 곳입니다.**

다음 상황에서 CLAUDE.md에 추가하세요:
- Claude가 같은 실수를 두 번 이상 반복할 때
- 코드 리뷰에서 Claude가 알았어야 할 규칙이 발견될 때
- 이전 세션과 동일한 설명을 또 타이핑하게 될 때
- 새 팀원에게도 알려줘야 할 내용일 때

### 크기 (Size)

- **파일당 200줄 이하**를 권장합니다.
- 파일이 길수록 컨텍스트를 더 소비하고 Claude의 지시 준수율이 떨어집니다.
- 내용이 많아지면 [`.claude/rules/`로 분리](#path-specific-rules)하거나 `@import`를 활용하세요.

> ⚠️ 주의: `@import`로 가져온 파일도 세션 시작 시 전체 로드되므로 컨텍스트 절약 효과는 없습니다. `.claude/rules/`의 경로 한정 규칙만 실제로 컨텍스트를 절약합니다.

### 구조 (Structure)

마크다운 헤더와 불릿을 활용해 관련 지시사항을 그룹화하세요. 밀도 있는 단락보다 구조화된 섹션이 Claude가 따르기 더 쉽습니다.

```markdown
# 프로젝트명

한두 줄 프로젝트 설명.

## 기술 스택
## 빌드 및 실행 명령어
## 코드 스타일
## 프로젝트 구조
## 워크플로우
## 주의사항
```

### 구체성 (Specificity)

검증 가능한 수준으로 구체적으로 작성하세요.

| ❌ 나쁜 예 | ✅ 좋은 예 |
|-----------|-----------|
| "코드를 깔끔하게 작성하세요" | "들여쓰기 2칸, 세미콜론 생략" |
| "변경사항을 테스트하세요" | "커밋 전 `npm test` 실행" |
| "파일을 정리하세요" | "API 핸들러는 `src/api/handlers/`에 위치" |
| "적절히 에러 처리하세요" | "API 호출은 전부 try-catch로 감싸세요" |

### 일관성 (Consistency)

두 규칙이 서로 모순되면 Claude가 임의로 하나를 선택합니다. CLAUDE.md 파일들을 주기적으로 검토해 상충하는 지시사항을 제거하세요.

### 포함하지 말아야 할 것

- API 키, 비밀번호 등 민감한 정보 (프로젝트 CLAUDE.md는 Git에 커밋됨)
- Claude가 코드에서 직접 추론할 수 있는 내용
- 불필요하게 장황한 설명

---

## 5. 프로젝트 유형별 템플릿

### 5-1. 웹 프론트엔드 (Next.js / React)

```markdown
# [프로젝트명] — 프론트엔드

Next.js 15 App Router 기반 [서비스 설명].

## 기술 스택
- Next.js 15 (App Router)
- TypeScript (strict mode)
- Tailwind CSS
- Zustand (상태관리)
- React Query (서버 상태)

## 빌드 및 실행
- 개발 서버: `npm run dev`
- 빌드: `npm run build`
- 타입 체크: `npm run typecheck`
- 린트: `npm run lint`
- 테스트: `npm test` (단일: `npm test -- --testPathPattern=파일명`)

## 프로젝트 구조
- `app/` — App Router 페이지 및 레이아웃
- `components/` — 재사용 UI 컴포넌트
- `lib/` — 유틸리티 및 헬퍼
- `store/` — Zustand 스토어
- `types/` — TypeScript 타입 정의

## 코드 스타일
- ES 모듈(`import/export`) 사용, CommonJS(`require`) 금지
- 가능하면 구조 분해 import 사용 (`import { foo } from 'bar'`)
- 컴포넌트는 기본 내보내기(default export), 유틸리티는 named export
- 인터페이스 이름 접두사 `I` 사용하지 않음
- 2칸 들여쓰기, 세미콜론 생략

## 워크플로우
- 일련의 코드 변경 완료 후 타입 체크 확인
- 전체 테스트 스위트 대신 단일 테스트 실행 (성능)
- 커밋 전 `npm run lint` 실행

## 주의사항
- `app/` 내 서버 컴포넌트에서는 `useState`, `useEffect` 사용 불가
- 환경변수는 `NEXT_PUBLIC_` 접두사 규칙 준수
- 이미지는 반드시 `next/image` 사용
```

---

### 5-2. 백엔드 API (Node.js / Express or Fastify)

```markdown
# [프로젝트명] — 백엔드 API

[서비스 설명] REST API 서버.

## 기술 스택
- Node.js 20+
- TypeScript
- Fastify (또는 Express)
- PostgreSQL + Prisma ORM
- Redis (캐싱)
- Jest (테스트)

## 빌드 및 실행
- 개발: `npm run dev`
- 빌드: `npm run build`
- 테스트: `npm test`
- DB 마이그레이션: `npx prisma migrate dev`
- DB 시드: `npm run seed`

## 프로젝트 구조
- `src/routes/` — API 라우트 핸들러
- `src/services/` — 비즈니스 로직
- `src/repositories/` — DB 접근 레이어
- `src/middleware/` — 공통 미들웨어
- `src/types/` — TypeScript 타입
- `prisma/` — DB 스키마 및 마이그레이션

## 코드 스타일
- 레이어드 아키텍처 준수 (Route → Service → Repository)
- 비즈니스 로직은 Service에만, DB 쿼리는 Repository에만
- 모든 API 핸들러는 try-catch로 감싸기
- HTTP 상태 코드 일관성 유지 (성공: 200/201, 클라이언트 에러: 4xx, 서버 에러: 5xx)

## API 규칙
- RESTful 명명 규칙: `/api/v1/users`, `/api/v1/users/:id`
- 요청/응답 타입은 `src/types/api/`에 정의
- 에러 응답 형식: `{ error: { code: string, message: string } }`

## 워크플로우
- 스키마 변경 시 반드시 마이그레이션 파일 생성
- 새 엔드포인트 추가 시 통합 테스트 작성
- 테스트는 실제 DB 대신 인메모리 SQLite 사용

## 주의사항
- `prisma/migrations/` 폴더 직접 수정 금지
- 환경변수 `.env.example` 항상 최신 상태 유지
- 인증이 필요한 라우트는 `authMiddleware` 적용 확인
```

---

### 5-3. 풀스택 모노레포

```markdown
# [프로젝트명] — 모노레포

[서비스 설명]. 프론트엔드와 백엔드를 포함한 모노레포.

## 기술 스택
- 패키지 매니저: pnpm (npm/yarn 사용 금지)
- 모노레포 도구: Turborepo
- 프론트엔드: `apps/web` — Next.js 15
- 백엔드: `apps/api` — Node.js + Fastify
- 공용 패키지: `packages/ui`, `packages/types`, `packages/utils`

## 빌드 및 실행
- 전체 개발: `pnpm dev`
- 특정 앱만: `pnpm --filter web dev`
- 전체 빌드: `pnpm build`
- 전체 테스트: `pnpm test`
- 패키지 추가: `pnpm --filter [app] add [package]`

## 프로젝트 구조
```
apps/
  web/          # Next.js 프론트엔드
  api/          # Fastify 백엔드
packages/
  ui/           # 공용 UI 컴포넌트
  types/        # 공용 TypeScript 타입
  utils/        # 공용 유틸리티 함수
  config/       # ESLint, TypeScript 등 공용 설정
```

## 워크플로우
- 공용 타입은 `packages/types`에만 정의
- UI 컴포넌트는 `packages/ui`에서 관리 후 각 앱에서 import
- 루트에서 실행: 전체 영향 작업
- 앱 디렉토리에서 실행: 해당 앱 한정 작업

## 주의사항
- 앱 간 직접 import 금지 (`apps/web`에서 `apps/api` import 불가)
- `packages/` 변경 시 의존하는 앱도 빌드 확인
- Turborepo 캐시 활용 (`--force` 플래그로 강제 재빌드)
```

---

### 5-4. Python 데이터/ML 프로젝트

```markdown
# [프로젝트명] — ML 파이프라인

[서비스 설명] 머신러닝 파이프라인.

## 기술 스택
- Python 3.11+
- uv (패키지 매니저, pip 사용 금지)
- PyTorch 2.x
- pandas, numpy, scikit-learn
- MLflow (실험 추적)
- pytest (테스트)

## 빌드 및 실행
- 환경 설정: `uv sync`
- 학습 실행: `python src/train.py --config configs/default.yaml`
- 평가: `python src/evaluate.py --checkpoint checkpoints/best.pt`
- 테스트: `pytest tests/ -v`
- 린트: `ruff check src/`
- 타입 체크: `mypy src/`

## 프로젝트 구조
- `src/` — 핵심 소스코드
- `configs/` — 실험 설정 파일 (YAML)
- `data/` — 원시 데이터 (Git 제외, `.gitignore` 확인)
- `notebooks/` — 탐색적 분석 노트북
- `tests/` — 단위 및 통합 테스트
- `checkpoints/` — 모델 체크포인트 (Git 제외)

## 코드 스타일
- 타입 힌트 필수 (함수 인자 및 반환값)
- docstring 형식: Google style
- 변수명: snake_case, 클래스명: PascalCase
- 매직 넘버 금지 — 상수로 분리

## 실험 관리
- 모든 실험은 MLflow로 추적
- 하이퍼파라미터는 YAML 설정 파일로 관리
- 재현성을 위해 시드 고정: `set_seed(42)`

## 주의사항
- `data/raw/` 직접 수정 금지 (원본 보존)
- 대용량 파일은 DVC로 관리
- GPU 메모리 부족 시 배치 크기 먼저 줄이기
```

---

### 5-5. 모바일 앱 (React Native)

```markdown
# [프로젝트명] — 모바일 앱

[서비스 설명] React Native 크로스플랫폼 앱.

## 기술 스택
- React Native 0.76+
- Expo SDK 52
- TypeScript
- React Navigation 7
- Zustand (상태관리)
- React Query (서버 상태)

## 빌드 및 실행
- 개발: `npx expo start`
- iOS 시뮬레이터: `npx expo run:ios`
- Android 에뮬레이터: `npx expo run:android`
- 테스트: `npm test`
- 타입 체크: `npx tsc --noEmit`

## 프로젝트 구조
- `app/` — Expo Router 페이지
- `components/` — 재사용 컴포넌트
- `hooks/` — 커스텀 훅
- `store/` — 상태 관리
- `services/` — API 통신

## 코드 스타일
- StyleSheet.create() 사용, 인라인 스타일 최소화
- 플랫폼별 코드: `Platform.OS === 'ios'` 또는 `.ios.tsx` / `.android.tsx` 확장자
- 절대 경로 import 사용 (`@/components/...`)

## 주의사항
- 네이티브 모듈 추가 시 `expo prebuild` 필요
- `expo-constants`로 환경변수 관리 (process.env 직접 사용 금지)
- 앱 스토어 제출 전 `eas build` 실행
```

---

### 5-6. 개인용 글로벌 설정 (`~/.claude/CLAUDE.md`)

```markdown
# 개인 전역 설정

## 커뮤니케이션
- 항상 한국어로 응답
- 기술 용어는 영어 원문 유지 (괄호 안에 설명 추가)
- 코드 설명은 간결하게, 필요시에만 상세 설명

## 코딩 선호도
- 함수형 패턴 선호 (가능하면 class 대신 함수)
- 주석은 "왜"를 설명, "무엇"은 코드로 표현
- 테스트는 given-when-then 패턴

## 워크플로우
- 변경 전 현재 상태 파악 우선
- 큰 변경은 작은 단계로 나눠 진행
- 불확실할 때는 먼저 질문

## 도구
- 패키지 매니저: npm 대신 pnpm 선호
- 편집기: VS Code (`.vscode/` 설정 존중)
- 터미널: zsh
```

---

## 6. 고급 기능

### 6-1. `@` Import 문법

CLAUDE.md 안에서 다른 파일을 참조할 수 있습니다. 참조된 파일은 세션 시작 시 CLAUDE.md와 함께 컨텍스트에 로드됩니다.

```markdown
# 프로젝트 개요
@README.md 참고

# 사용 가능한 npm 명령어
@package.json

# Git 워크플로우
@docs/git-instructions.md

# 개인 설정 (Git 미포함)
@~/.claude/my-project-instructions.md
```

- 상대 경로는 파일이 위치한 곳 기준으로 해석됩니다.
- 최대 5단계까지 재귀 import 가능합니다.
- 홈 디렉토리(`~/`) 경로도 지원합니다.

### 6-2. `.claude/rules/` — 경로 한정 규칙

큰 프로젝트에서 파일 타입이나 디렉토리별로 규칙을 분리할 수 있습니다.

**디렉토리 구조 예시:**
```
your-project/
├── .claude/
│   ├── CLAUDE.md           # 메인 프로젝트 지시사항
│   └── rules/
│       ├── code-style.md   # 코드 스타일 가이드라인
│       ├── testing.md      # 테스트 컨벤션
│       ├── security.md     # 보안 요구사항
│       ├── frontend/
│       │   └── react.md    # React 관련 규칙
│       └── backend/
│           └── api.md      # API 설계 규칙
```

**경로 한정 규칙 작성 예시** (`.claude/rules/api.md`):
```markdown
---
paths:
  - "src/api/**/*.ts"
  - "src/routes/**/*.ts"
---

# API 개발 규칙

- 모든 API 엔드포인트에 입력 유효성 검사 포함
- 표준 에러 응답 형식 사용
- OpenAPI 문서 주석 포함
```

**지원 glob 패턴:**

| 패턴 | 매칭 |
|------|------|
| `**/*.ts` | 모든 디렉토리의 TypeScript 파일 |
| `src/**/*` | `src/` 하위 모든 파일 |
| `*.md` | 프로젝트 루트의 마크다운 파일 |
| `src/**/*.{ts,tsx}` | TypeScript와 TSX 파일 |

> 경로 한정 규칙은 Claude가 해당 패턴의 파일을 읽을 때만 컨텍스트에 로드됩니다. 컨텍스트 절약에 효과적입니다.

### 6-3. `AGENTS.md` 공존

다른 AI 코딩 도구(Cursor, Zed 등)용 `AGENTS.md`가 이미 있다면, `CLAUDE.md`에서 임포트하여 중복을 피할 수 있습니다.

```markdown
@AGENTS.md

## Claude Code 전용 지침

`src/billing/` 변경 시 plan mode 사용.
```

### 6-4. HTML 주석 활용

`CLAUDE.md`의 블록 수준 HTML 주석은 Claude의 컨텍스트에 주입되기 전에 제거됩니다. 인간 유지보수자를 위한 메모를 컨텍스트 토큰 낭비 없이 남길 수 있습니다.

```markdown
# 코딩 규칙

<!-- 
유지보수 노트: 이 규칙은 2025-01 레거시 마이그레이션 이후 추가됨.
다음 메이저 버전 업그레이드 시 재검토 예정.
-->

- TypeScript strict mode 사용
```

---

## 7. 운영 및 유지보수

### 빠른 시작: `/init` 명령어

새 프로젝트에서 Claude Code 세션을 열고 `/init`을 실행하면 코드베이스를 분석해 초기 CLAUDE.md를 자동 생성합니다. 이후 불필요한 내용을 제거하고 프로젝트에 맞게 보완하세요.

```bash
# 인터랙티브 모드 (CLAUDE.md, skills, hooks 선택적 생성)
CLAUDE_CODE_NEW_INIT=1 claude
# → /init 실행
```

### `#` 키로 즉시 추가

Claude Code 대화 중 `#` 키를 누르면 현재 지시사항을 관련 CLAUDE.md에 자동으로 통합할 수 있습니다.

### 살아있는 문서로 관리

CLAUDE.md는 코드베이스처럼 지속적으로 개선해야 합니다.

**Boris Cherny (Claude Code 창시자) 방식:**
> "Claude가 실수할 때마다 CLAUDE.md에 추가한다. 그러면 다음에는 같은 실수를 하지 않는다."

**주기적 업데이트 시점:**
- 커밋/PR 완료 후: `이전 커밋의 변경사항을 확인해서 CLAUDE.md에 추가할 내용이 있으면 제시해줄래?`
- 스프린트 종료 후: 새 패턴, 팀 규칙 변경사항 반영
- 코드 리뷰 후: 발견된 규칙 위반 방지를 위해 추가

### `/memory` 명령어

현재 세션에서 로드된 모든 CLAUDE.md, CLAUDE.local.md, rules 파일 목록을 확인하고 편집할 수 있습니다.

---

## 8. 자주 하는 실수 & 디버깅

### Claude가 CLAUDE.md를 따르지 않을 때

1. `/memory` 실행 → 해당 파일이 로드 목록에 있는지 확인
2. 파일 위치가 올바른지 확인 ([위치 계층 구조](#2-파일-위치-및-범위) 참고)
3. 지시사항을 더 구체적으로 수정
4. 여러 CLAUDE.md 파일 간 상충하는 지시사항 확인

### 파일이 너무 커질 때

- 200줄 초과 → `.claude/rules/`로 분리
- 경로 한정 규칙 활용 → 해당 파일 작업 시에만 로드
- `@import`는 컨텍스트 절약 효과 없음 (세션 시작 시 전체 로드)

### 민감 정보 노출 방지

- 프로젝트 CLAUDE.md는 Git에 커밋되므로 API 키, 비밀번호 절대 포함 금지
- 개인 설정은 반드시 `CLAUDE.local.md`에 작성하고 `.gitignore`에 추가

### 모노레포에서 불필요한 파일 제외

```json
// .claude/settings.local.json
{
  "claudeMdExcludes": [
    "**/other-team/CLAUDE.md",
    "/home/user/monorepo/legacy/.claude/rules/**"
  ]
}
```

---

## 참고 링크

- [공식 문서: How Claude remembers your project](https://docs.anthropic.com/en/docs/claude-code/memory)
- [공식 문서: Best practices](https://docs.anthropic.com/en/docs/claude-code/best-practices)
- [Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code/skills)
- [Claude Code Settings](https://docs.anthropic.com/en/docs/claude-code/settings)
