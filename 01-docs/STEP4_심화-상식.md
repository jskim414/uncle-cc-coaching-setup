# STEP 4. 클로드 코드 심화 상식

> 기초(STEP 3)를 익히신 뒤, AI를 자는 동안에도 굴리는 수준으로 올라가기 위한 8가지 주제입니다.
> 대상: CLAUDE.md v1 작성·MCP 1개 연결이 끝난 대표님
> 분량: 혼자 읽는 데 25분, 실습 포함 시 3~4시간

---

## 🎯 이 문서를 읽는 방법

- 각 부마다 **한 줄 정의 → Before/After → 최소 실습 → 실전 레시피 → 함정**
- 1~3부(파일 리터러시)는 빠르게 훑고, 4~8부를 본편으로 읽으시면 됩니다
- 오늘 다 하실 필요는 없습니다. 본인 업무에 와닿는 1~2개부터 골라 시작하세요

---

## 📌 핵심 메시지 한 줄

> **기초는 "AI와 말 통하기", 심화는 "AI에게 사업을 굴리게 하기"입니다.**

- 기초 = CLAUDE.md·폴더·MCP·훅 (혼자 쓰는 수준)
- 심화 = API·Cron·Git·컨텍스트 관리 (자동화·확장·팀 공유 수준)

---

# 1부. Markdown 기본 문법

## 1.1 한 줄 정의

**텍스트에 최소한의 기호로 구조를 주는 법**입니다. 클로드 코드의 모든 문서(CLAUDE.md·SKILL.md·회의록·블로그)는 Markdown으로 씁니다.

## 1.2 외울 건 7개

| 기호 | 의미 | 예시 |
|------|------|------|
| `#` `##` `###` | 제목 (1·2·3단계) | `## 1. 나는 누구` |
| `**굵게**` | 강조 | `**절대 금지**` |
| `*기울임*` | 약한 강조 | `*참고*` |
| `- ` / `1. ` | 목록(순서 없음·있음) | `- 매주 회고` |
| `[이름](주소)` | 링크 | `[공식 문서](https://claude.com)` |
| `` ```코드``` `` | 코드 블록 | 프롬프트·명령어 감쌀 때 |
| `>` | 인용 | `> 중요 원칙` |

## 1.3 CLAUDE.md 3분 완성 뼈대

```markdown
# CLAUDE.md

## 1. 나는 누구
- 이름: 홍길동
- 업종: B2B SaaS 대표
- 톤: 간결·전문·과장 금지

## 2. 자주 하는 업무
- 주간 회고 작성
- 제안서 초안
- 경쟁사 리서치

## 3. 금기
- 확정 안 된 숫자 쓰지 않기
- 경쟁사 실명 비방 금지
```

## 1.4 함정

- 제목(`#`)과 본문(`-`) 사이에 **빈 줄**이 없으면 깨집니다 → 항상 빈 줄 1개
- 한글 입력기에서 `-` 뒤에 **반각 공백**을 써야 합니다 (전각 공백은 X)

---

# 2부. JSON 기본 문법

## 2.1 한 줄 정의

**컴퓨터가 읽는 구조화된 데이터 포맷**입니다. `settings.json`, `mcp.json`은 전부 JSON입니다.

## 2.2 기본 규칙 5개

```json
{
  "이름": "값",
  "숫자": 42,
  "참거짓": true,
  "목록": ["A", "B", "C"],
  "중첩": {
    "하위이름": "하위값"
  }
}
```

1. 전체를 `{ }` 중괄호로 감쌉니다
2. 키·값은 `"쌍따옴표"`로 감쌉니다 (작은따옴표 X)
3. 콜론 `:` 으로 키와 값을 연결합니다
4. 항목 사이에 쉼표 `,` — **마지막 항목 뒤엔 쉼표 금지**
5. 주석은 없습니다 (원칙상)

## 2.3 실전 예시 — MCP 설정 블록

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_TOKEN": "secret_xxxxx"
      }
    }
  }
}
```

## 2.4 가장 흔한 오류 3가지

| 증상 | 원인 | 해결 |
|------|------|------|
| `Unexpected token` | 마지막 항목 뒤 쉼표 | 마지막 쉼표 제거 |
| `Unexpected string` | 쌍따옴표 누락 | `"이름"`으로 감싸기 |
| 전체 작동 안 함 | 중괄호·대괄호 개수 불일치 | VS Code가 색상·들여쓰기로 표시 |

> 💡 VS Code는 JSON 오류를 빨간 줄로 자동 표시합니다. 확장 **Prettier** 설치 후 저장 시 자동 포맷을 켜면 편합니다.

---

# 3부. .env (환경변수)

## 3.1 한 줄 정의

**비밀번호·API 키를 코드와 분리**해서 보관하는 파일입니다. 실수로 GitHub에 업로드되는 사고를 막는 방어선입니다.

## 3.2 왜 필요한가

| Before (하드코딩) | After (.env) |
|------------------|--------------|
| 코드에 `NOTION_TOKEN = "secret_xxx"` 직접 작성 → GitHub push → 봇이 5분 안에 탈취 | 코드엔 `NOTION_TOKEN=$ENV` 참조만 → .env는 GitHub 업로드 안 됨 |
| 토큰을 바꿀 때 코드 20곳 수정 | .env 한 줄만 수정 |

> 🚨 매일 GitHub 공개 저장소에서 수천 개의 API 키가 탈취됩니다. AWS·OpenAI 지갑이 털리는 가장 흔한 경로입니다.

## 3.3 최소 실습 (3분)

**.env 파일 생성**
```
# claude-workspace/.env
NOTION_TOKEN=secret_abc123
OPENAI_API_KEY=sk-xxxxx
GOOGLE_API_KEY=AIzaxxxxx
```

**.gitignore에 반드시 추가**
```
.env
.env.local
*.key
```

> ⚠️ `.env`는 **절대 GitHub에 push하지 않습니다**. `.gitignore`에 추가해 Git이 무시하게 만드는 것이 핵심입니다.

## 3.4 클로드 코드에서 불러오기

**mcp.json에서**
```json
{
  "env": {
    "NOTION_TOKEN": "${env:NOTION_TOKEN}"
  }
}
```

**터미널에서 세션별**
```bash
export NOTION_TOKEN=secret_xxx
claude
```

## 3.5 함정

- `.env`를 이미 한 번 push하셨다면 토큰은 이미 노출됐습니다 → **즉시 재발급**
- `.env.example` (더미 값)은 push해도 됩니다 → 팀원용 가이드
- 운영 서버에서는 `.env` 대신 **secret manager**(AWS Secrets Manager 등)를 쓰세요

---

# 4부. 컨텍스트 관리

## 4.1 한 줄 정의

**AI가 한 번에 기억할 수 있는 정보량(컨텍스트 윈도우)을 관리**하는 기술입니다. 긴 대화에서 AI 품질이 떨어지는 근본 원인이 여기에 있습니다.

## 4.2 왜 가장 중요한가

| Before | After |
|--------|-------|
| "AI가 갑자기 멍청해졌다" → 대화 포기 | `/compact` 한 번으로 복구 |
| 중요한 대화가 세션 종료로 증발 | 핵심을 CLAUDE.md에 **승격** |
| 매번 처음부터 설명 | **프로젝트별 CLAUDE.md**로 맥락 분리 |

## 4.3 컨텍스트 윈도우 감각

| 모델 | 윈도우 크기 | 대략 한국어 분량 |
|------|-----------|-----------------|
| Sonnet 4.6 | 200K 토큰 | A4 약 300쪽 |
| Opus 4.7 (1M) | 1M 토큰 | A4 약 1,500쪽 |

채워지는 주범: 파일 첨부(`@` 참조) · 긴 로그 · 전체 코드 읽기

## 4.4 3가지 핵심 명령어

### `/clear` — 완전 리셋
- 언제: 새 주제로 넘어갈 때, 대화가 엉망일 때
- 효과: 모든 맥락 초기화. CLAUDE.md는 재로딩됨
- 주의: 되돌릴 수 없음

### `/compact` — 대화 압축
- 언제: 긴 대화 중반쯤 AI 답이 이상해질 때
- 효과: 지금까지 대화를 AI가 요약해 그 요약만 남김
- 장점: 맥락은 유지, 윈도우는 확보

### `/context` (또는 하단 표시) — 현재 사용량 확인
- 언제: 수시로 확인
- 80%를 넘으면 `/compact` 실행

## 4.5 서브디렉토리 CLAUDE.md

**구조**
```
claude-workspace/
├── CLAUDE.md                ← 전체 나 (공통)
├── 02-projects/
│   └── Q2-제안서/
│       └── CLAUDE.md        ← 이 프로젝트 전용 맥락
└── 03-areas/
    └── blog/
        └── CLAUDE.md        ← 블로그 전용 톤·규칙
```

클로드 코드는 현재 폴더에서 위로 올라가며 CLAUDE.md를 찾아 전부 합쳐 로딩합니다. 프로젝트별로 다른 톤·규칙을 쓸 수 있습니다.

## 4.6 함정

| # | 함정 | 해결 |
|---|------|------|
| 1 | `@` 로 100MB PDF 첨부 → 윈도우 순삭 | 핵심 챕터만 추출하거나 요약본 사용 |
| 2 | `/clear` 남발 → 맥락 매번 처음부터 | `/compact`를 먼저 시도 |
| 3 | CLAUDE.md 비대화 (500줄 넘김) | 참조 파일로 빼고 `@`로 호출 |
| 4 | 긴 로그 통째 붙여넣기 | `head -50` 등으로 일부만 |

---

# 5부. Git / GitHub 최소

## 5.1 한 줄 정의

**Git = 내 파일 버전 관리 / GitHub = 그것을 온라인에 올려 공유하는 창고**입니다. 오픈소스 생태계 접근의 열쇠입니다.

## 5.2 왜 킬러인가

혼자 다 만드실 필요 없습니다. 이미 누가 만들어 뒀습니다.

| 검색어 | 얻는 것 |
|--------|---------|
| `awesome-claude-code` | 전 세계 사용자가 큐레이션한 스킬·프롬프트·MCP |
| `claude-code mcp` | MCP 서버 수백 개 |
| `claude-code skills` | 복사해서 바로 쓰는 SKILL.md 템플릿 |
| `prompt engineering` | 업무별 프롬프트 레시피 |

## 5.3 최소 3명령어 + 3가지 행위

**명령어**
```bash
git clone https://github.com/xxx/yyy.git   # 남의 저장소 가져오기
git add . && git commit -m "변경"           # 내 변경사항 기록
git push                                    # 온라인에 올리기
```

**3가지 행위**
1. **Clone** — 남이 만든 스킬·MCP를 내 PC로
2. **Gist** — 짧은 파일(CLAUDE.md·SKILL.md) 공유
3. **Fork** — 남의 저장소를 복사해 내 것으로 개조

## 5.4 실전 시나리오

### 시나리오 1. 스킬 가져오기
```bash
cd ~/claude-workspace/.claude/skills/
git clone https://github.com/xxx/awesome-skill.git
```

### 시나리오 2. 내 CLAUDE.md Gist 공유
1. https://gist.github.com 접속
2. 파일명 `CLAUDE.md`, 내용 붙여넣기
3. "Create secret gist" (링크 아는 사람만)
4. URL 복사 → 블로그·팀에 공유

### 시나리오 3. Fork로 내 버전 만들기
1. 마음에 드는 저장소에서 우측 상단 **Fork** 클릭
2. 내 계정 아래로 복사됨
3. Clone → 수정 → Push

## 5.5 준비물

- **GitHub 계정** — https://github.com 무료 가입
- **SSH 키 또는 Personal Access Token** — 인증용 (한 번 설정)

## 5.6 함정

- 민감 정보가 있는 저장소를 Public으로 올리지 마세요 — 유출 사고의 1순위입니다
- 커밋 메시지 "update"·"fix"만 쓰면 3개월 뒤에 본인도 못 알아봅니다 → "왜 바꿨나"를 한 문장으로
- 큰 바이너리(이미지·영상)는 Git 저장소를 부풀립니다 → 별도 저장소로 분리

---

# 6부. API 호출

## 6.1 한 줄 정의

**다른 서비스(remove.bg, OpenAI, Google 번역 등)의 기능을 클로드 코드가 직접 호출**하게 만드는 방법입니다.

## 6.2 Anthropic API vs Claude Code

| 구분 | Claude Code | Anthropic API |
|------|-------------|---------------|
| 사용법 | 터미널에서 `claude` 대화형 | 프로그램 안에서 호출 |
| 과금 | Max 플랜 구독 ($100/월) | 사용한 만큼 종량제 |
| 용도 | 업무 대화·스킬 운영 | **자동화·대량 처리** |

> 💡 대부분은 **Claude Code만으로 충분**합니다. API는 "내 앱을 만들 때" 또는 "대량 배치 처리할 때" 씁니다.

## 6.3 최소 실습 — curl 한 줄로 API 호출

**이미지 BG 제거 (remove.bg)**
```bash
curl -H "X-Api-Key: $REMOVEBG_API_KEY" \
     -F "image_file=@input.jpg" \
     -f https://api.remove.bg/v1.0/removebg \
     -o output.png
```

**OpenAI로 이미지 생성**
```bash
curl https://api.openai.com/v1/images/generations \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt":"사무실 고양이","size":"1024x1024"}'
```

> 💡 클로드 코드 안에서 이 curl을 직접 실행하게 시키면, AI가 알아서 API를 호출해 결과를 가져옵니다.

## 6.4 먼저 쓰실 API 5개

| 용도 | 서비스 | 월 무료량 |
|------|--------|-----------|
| 이미지 BG 제거 | remove.bg | 50장 |
| 이미지 생성 | OpenAI DALL-E / Gemini Imagen | 종량제 |
| 음성 → 텍스트 | OpenAI Whisper | 종량제(저렴) |
| 번역 | DeepL | 50만 자 |
| 경쟁사 SEO 분석 | Serper / SerpAPI | 100~2,500회 |

## 6.5 API 키 발급·관리 절차

1. 서비스 가입 → 대시보드에서 "API Keys" 메뉴
2. "Create new key" → 복사 (한 번만 표시됨)
3. `.env`에 저장 (3부 참조)
4. 사용량·과금 알림 설정 (필수 — 방어선)

## 6.6 함정

| # | 함정 | 해결 |
|---|------|------|
| 1 | 키 노출 → 하룻밤에 1만 원이 100만 원으로 | 사용량 알림 + 일일 한도 설정 |
| 2 | curl 문법 틀림 | 클로드 코드에 "이거 API 호출해 줘"로 위임 |
| 3 | Rate limit 초과 | 유료 플랜 전환 또는 재시도 로직 |
| 4 | 민감 데이터 API 전송 | 제공사 **데이터 처리 약관** 반드시 확인 |

---

# 7부. Cron 자동화

## 7.1 한 줄 정의

**정해진 시간에 자동으로 명령어를 실행하는 OS 기본 기능**입니다. 대표님이 주무시는 동안 AI가 일하게 만드는 장치입니다.

## 7.2 왜 킬러인가

| Before (수동) | After (Cron) |
|--------------|--------------|
| 매일 아침 9시 Notion에서 어제 업무 확인 후 브리핑 | 7시에 완성된 브리핑이 이메일로 도착 |
| 주말마다 경쟁사 블로그 수동 체크 | 월요일 9시에 요약이 Slack에 올라와 있음 |
| 월말에 재무·KPI 수동 집계 | 말일 오후 6시 자동 생성 → 승인만 |

## 7.3 OS별 실행

### Windows — 작업 스케줄러

**GUI 방식 (권장)**
1. 시작 메뉴 → "작업 스케줄러" 실행
2. 우측 **기본 작업 만들기**
3. 이름: "매일 브리핑" / 트리거: 매일 7:00
4. 동작: 프로그램 시작
   - 프로그램: `claude`
   - 인수: `--print "오늘 브리핑 만들어 줘"`
   - 시작 위치: `C:\Users\대표님\claude-workspace`

**PowerShell 한 줄 등록**
```powershell
schtasks /create /tn "daily-briefing" /tr "claude --print '브리핑'" /sc daily /st 07:00
```

### macOS — crontab

```bash
crontab -e
```
에디터가 열리면
```
0 7 * * * cd ~/claude-workspace && claude --print "오늘 브리핑" >> ~/briefing.log
```

## 7.4 Cron 표현식

```
분 시 일 월 요일  명령어
0  7  *  *  *     → 매일 오전 7시
```

**자주 쓸 5개 패턴**

| 언제 | 표현식 |
|------|--------|
| 매일 오전 7시 | `0 7 * * *` |
| 평일(월~금) 9시 | `0 9 * * 1-5` |
| 월요일 아침 8시 | `0 8 * * 1` |
| 매 시간 정각 | `0 * * * *` |
| 매월 1일 9시 | `0 9 1 * *` |

> 💡 헷갈리면 https://crontab.guru 에 표현식을 붙여넣으면 한글로 번역됩니다.

## 7.5 실전 레시피

### 평일 아침 브리핑
```
0 7 * * 1-5 cd ~/claude-workspace && claude --print "/daily-briefing" >> ~/briefing.log
```

### 주간 KPI 리포트 (월요일 8시)
```
0 8 * * 1 cd ~/claude-workspace && claude --print "지난주 매출·회의록·블로그 실적 요약 → reports/weekly.md로 저장"
```

### 경쟁사 블로그 체크 (일요일 저녁 8시)
```
0 20 * * 0 cd ~/claude-workspace && claude --print "@04-resources/competitors.md 목록의 블로그 최신글 3개씩 요약"
```

## 7.6 함정

| # | 함정 | 해결 |
|---|------|------|
| 1 | 환경변수 로딩 안 됨 | `source ~/.env &&` 추가 또는 전체 경로 지정 |
| 2 | PC가 꺼져 있으면 실행 안 됨 | 절전모드 해제 또는 클라우드로 이전 |
| 3 | 실패 감지 불가 | 항상 `>> ~/cron.log 2>&1` 로 로그 남기기 |
| 4 | API 비용 폭탄 | 빈도 주의 — 매분 실행 금지 |
| 5 | 민감 정보 자동 전송 사고 | 훅으로 차단 장치 병행 |

## 7.7 한 단계 위 — n8n · Zapier

| 도구 | 장점 | 단점 | 시점 |
|------|------|------|------|
| **Cron** | 무료, OS 기본 | 내 PC 켜져 있어야 | 지금 바로 |
| **n8n** | 시각적·300+ 연동 | 초기 설치 필요 | 팀 확장 시 |
| **Zapier** | 가장 쉬움 | 월 $20~ | 테스트용 |

---

# 8부. 여러 AI 병용 전략

## 8.1 한 줄 정의

**AI마다 강한 영역이 다릅니다**. 클로드 코드를 메인으로 쓰시되 다른 AI에 역할을 분담합니다.

## 8.2 4대 AI 역할 분담표

| AI | 강점 | 용도 |
|----|------|------|
| **Claude Code** | 파일·폴더·자동화·추론 | **메인 업무 운영 체계** |
| **ChatGPT** | 일상 대화·아이디어 발산·이미지 | 빠른 검색·브레인스토밍·DALL-E |
| **Gemini** | 구글 생태계(Workspace)·무료 | Gmail·Docs·Sheets 내부 작업 |
| **Perplexity** | 실시간 웹 검색·인용 표시 | 리서치·팩트 체크·뉴스 |

## 8.3 하루 동선 예시

```
07:00  Cron이 Claude Code로 브리핑 자동 생성
09:00  Perplexity로 업계 뉴스 5분 스캔
11:00  ChatGPT와 브랜드 메시지 10개 브레인스토밍
14:00  Claude Code로 제안서 초안 + 리뷰 서브에이전트
16:00  Gemini로 Gmail 답장 일괄 처리
18:00  Claude Code로 오늘 회고 + 내일 계획
```

## 8.4 Antigravity + Claude Code 조합

Google이 2026년 공개한 **Agent-First IDE**. MCP 표준을 공유합니다.
- **Gemini 3 Pro/Flash 무료** 사용
- 내장 터미널에서 `claude` 실행 → 두 AI 병행
- Google 계정 연동 → Workspace 즉시 연결

> ⚠️ 안정성 이슈 주의. 메인은 VS Code, 실험용으로 Antigravity를 권장합니다.

## 8.5 로컬 LLM (Ollama) — 민감 데이터 전용

**상황**: 고객 개인정보·재무·법무 문서 → 외부 전송 금지

**Ollama**: 내 PC에서 돌리는 오픈소스 AI
- https://ollama.com 다운로드
- `ollama run llama3.1` 한 줄로 실행
- 성능은 Claude보다 떨어지지만 **100% 로컬**

**권장 조합**
- 민감 데이터 1차 분석 → Ollama
- 최종 가공 (익명화 후) → Claude Code

## 8.6 함정

- 같은 질문을 4개 AI에 모두 던지지 마세요 → 시간·비용 낭비. 역할 구분해 한 곳에서 처리
- 중요 자료를 무료 AI에 올리지 마세요 → 학습 대상이 될 수 있습니다(약관 확인)
- Claude Max + ChatGPT Plus + Gemini Advanced 중복 구독은 본인 패턴 보고 2개로 정리하세요

---

# 🎯 자가 체크리스트

- [ ] Markdown 기호 7개 (`#`, `**`, `-`, `[]()`, `` ``` ``, `>`, `*`)
- [ ] JSON 기본 규칙 5개 (중괄호·쌍따옴표·콜론·쉼표·주석 없음)
- [ ] `.env` 파일의 역할과 `.gitignore` 연결
- [ ] 컨텍스트 윈도우 개념 + `/clear` vs `/compact` 차이
- [ ] 서브디렉토리 CLAUDE.md 로딩 순서
- [ ] Git 3명령어 (`clone`·`commit`·`push`)
- [ ] Gist로 CLAUDE.md 공유하는 방법
- [ ] Claude Code vs Anthropic API 차이
- [ ] API 키 발급 → `.env` 저장 → 호출 플로우
- [ ] Cron 표현식 `0 7 * * *` 해석
- [ ] 자동화 실전 레시피 중 1개 본인 버전 구상
- [ ] 4대 AI 역할 분담

**8개 이상** ✅ 이면 심화 수료 수준입니다.

---

# 🚦 자주 나오는 오해

| # | 오해 | 응답 |
|---|------|------|
| 1 | "API 쓰려면 프로그래밍 배워야 하나요?" | curl 한 줄 + `.env` 만 알면 시작 가능합니다 |
| 2 | "Cron은 리눅스 관리자 도구 아닌가요?" | Windows 작업 스케줄러로 동일 기능. GUI 클릭만 |
| 3 | "GitHub은 개발자만 쓰는 곳 아닌가요?" | Gist·Fork·Clone만 쓰면 업무 템플릿 창고입니다 |
| 4 | "`.env` 만들면 안전한가요?" | `.gitignore` 추가가 핵심입니다. 안 하면 무의미합니다 |
| 5 | "Opus 1M 쓰면 다 해결되지 않나요?" | 비용 폭증. `/compact` 습관이 우선입니다 |
| 6 | "Claude가 최고니 ChatGPT는 끊을까요?" | 역할 분담이 답입니다. 브레인스토밍은 ChatGPT가 빠릅니다 |
| 7 | "Cron은 매분 돌려야 실시간이죠?" | 매분 실행 금지 — API 비용 폭탄. 최소 10분 간격 |
| 8 | "Ollama가 무료니 Claude 끊을까요?" | 성능 차이가 큽니다. 민감 데이터 전용으로 병행하세요 |

---

# 🔗 다음 단계

1. **`.env` 세팅 + API 1개 연결** — remove.bg 또는 OpenAI (30분)
2. **첫 Cron 등록** — 레시피 3개 중 하나 복사 (1시간)
3. **GitHub 계정 + Gist 1개** — 내 CLAUDE.md 공유 (20분)
4. **컨텍스트 습관** — 오늘부터 `/compact` 매일 1회 이상 써 보기

그다음은 **캡스톤 프로젝트**(본인 업무 자동화 1건 완성)와 **팀 공유 v1**(AGENTS.md·공통 스킬·가이드라인)입니다.

---

## 📎 공식 참고

- Claude 공식 문서 — https://docs.claude.com
- crontab 번역기 — https://crontab.guru
- Anthropic 에이전트 패턴 — https://www.anthropic.com/research/building-effective-agents
- Ollama — https://ollama.com

---

**이 시리즈의 끝 — 이후는 캡스톤·팀 확장으로 연결됩니다.**
