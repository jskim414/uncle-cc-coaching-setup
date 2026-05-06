# STEP 1. 완전 설치·세팅 가이드

> 처음부터 순서대로 설치해 클로드 코드를 쓸 수 있는 환경을 만듭니다.
> 대상: 비개발자 대표님 / Windows 10·11 기본, macOS 병기
> 소요 시간: 40~60분 (플랜 가입 포함)

---

## 🎯 무엇을 설치하나요?

| 순서 | 항목 | 역할 | 필수 여부 |
|------|------|------|-----------|
| 1 | **Claude Max 플랜** | 클로드 코드를 쓸 수 있게 해 주는 열쇠 | 🔴 필수 |
| 2 | **Git** | Windows에서 클로드 코드가 내부적으로 쓰는 Bash 셸 | 🔴 Windows 필수 |
| 3 | **Node.js** | MCP·확장 도구 실습 시 필요 | 🟡 선택 |
| 4 | **Claude Code 본체** | AI 코딩 에이전트 | 🔴 필수 |
| 5 | **IDE** (VS Code / Antigravity) | 코드를 눈으로 보고 편집하는 창 | 🟢 강력 권장 |

> **설치 순서 요약**: 플랜 가입 → Git → (선택: Node.js) → Claude Code → 인증 → IDE

---

## 1. Claude Max 플랜 가입 (약 5분)

### 왜 Max 이상인가
- **Free / Pro** 는 사용량 한도 초과로 자주 멈춥니다
- **Max 5x ($100/월)** 부터 실무에 쓸 만합니다
- **Max 20x ($200/월)** 는 하루 종일 돌리시는 분께 추천합니다

| 플랜 | 월 요금 | 사용성 | 추천 대상 |
|------|--------|-------|-----------|
| Free | $0 | ❌ 사용 불가 | — |
| Pro | $20 | ⚠️ 한도로 자주 멈춤 | 맛보기용 |
| **Max 5x** | **$100** | ✅ 실무 가능 | **대부분의 대표님** |
| Max 20x | $200 | ✅ 넉넉 | 하루 종일 사용 |

### 가입 방법
1. https://claude.com/pricing 접속
2. 우측 상단 **Sign in** → 구글 계정 등으로 로그인
3. 왼쪽 메뉴 **Settings** → **Plans & billing**
4. **Max 5x** 또는 **Max 20x** 선택 → 카드 결제

> 💡 결제는 **해외 결제가 가능한 신용카드**를 씁니다. 국내 체크카드는 실패하는 경우가 많습니다.
> 💡 부담되면 언제든 다운그레이드할 수 있습니다(한 달 단위 과금).

---

## 2. Git 설치 (Windows 필수, 약 5분)

> Windows에서 클로드 코드를 네이티브로 실행하려면 Git이 반드시 필요합니다.
> (WSL을 쓰시는 분은 이 단계를 건너뛰어도 됩니다)

### 설치 방법
1. https://git-scm.com/downloads/win 접속
2. **64-bit Git for Windows Setup** 다운로드
3. `.exe` 파일 실행
4. 설치 옵션은 **모두 기본값 Next** → Install
5. 완료 후 **Finish**

### 설치 확인
1. `Windows + R` → `cmd` 입력 → Enter
2. 아래 명령어 입력
   ```bash
   git --version
   ```
3. `git version 2.xx.x` 같은 글자가 뜨면 성공입니다.

> ⚠️ `'git'은(는) 내부 또는 외부 명령... 이 아닙니다` 오류가 나면, 기존 프롬프트 창을 닫고 **새로 열어서** 다시 시도하세요.

---

## 3. Node.js 설치 (선택, 약 5분)

> 클로드 코드 네이티브 설치만 쓰시면 Node.js는 필요 없습니다.
> 다만 아래 경우엔 설치를 권장합니다.
> - npm 방식으로 설치하고 싶을 때
> - MCP 서버를 직접 구동할 때
> - npm 기반 확장 도구를 함께 쓸 때

### 설치 방법
1. https://nodejs.org/en/download 접속
2. **LTS 버전**(20.x 이상) 다운로드
3. `.msi` 파일 실행 → 기본값 Next 연타
4. 설치 완료

### 설치 확인
```bash
node --version
npm --version
```
각각 `v20.x.x`, `10.x.x` 같은 숫자가 뜨면 성공입니다.

---

## 4. Claude Code 설치 (약 3분)

### 네이티브 인스톨러 (권장)

**Windows — PowerShell**
1. `Windows + X` → **터미널** 또는 **PowerShell** 실행
2. 아래 명령어 한 줄 붙여넣기 → Enter
   ```powershell
   irm https://claude.ai/install.ps1 | iex
   ```
3. 1분 내 설치 완료

**macOS / Linux / WSL**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows — CMD (PowerShell이 안 될 때)**
```bash
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

> ⚠️ `'irm' is not recognized` 오류가 나면 CMD에서 PowerShell 명령을 쓴 것입니다. **PowerShell을 새로 열어** 다시 시도하세요.
> ⚠️ `The token '&&' is not a valid...` 오류가 나면 반대로 PowerShell에서 CMD 명령을 쓴 것입니다. CMD에서 시도하세요.
> 💡 프롬프트 앞이 `PS C:\` 면 PowerShell, `C:\` 면 CMD 입니다.

### 설치 확인

**새 터미널**을 열고
```bash
claude --version
```
`2.1.xxx` 같은 버전이 뜨면 성공입니다.

더 꼼꼼한 진단이 필요하면
```bash
claude doctor
```

---

## 5. 최초 실행·인증 (약 2분)

1. 아무 폴더에서 터미널을 엽니다
   - Windows: 폴더 안 빈 공간 **Shift + 우클릭** → **여기서 터미널 열기**
2. 아래 명령어 입력
   ```bash
   claude
   ```
3. 브라우저가 자동으로 열리며 로그인 화면이 나옵니다
4. **1단계에서 가입한 Max 플랜 계정**으로 로그인
5. "Authorize Claude Code" 클릭 → 터미널로 복귀
6. `>` 기호가 깜빡이면 성공입니다

> 💡 처음 실행하면 설정 질문(테마·약관 등)이 나옵니다. 모두 Enter로 기본값 선택하셔도 됩니다.

---

## 6. IDE 설치 (약 10분)

> IDE는 코드를 눈으로 보면서 편집하는 프로그램입니다.
> 클로드 코드는 터미널만으로도 돌아가지만, IDE와 함께 쓰면 훨씬 편합니다.

### 선택지 한눈에 보기

| IDE | 특징 | 추천 대상 |
|-----|------|-----------|
| **VS Code** | 표준 개발 툴. 가볍고 안정적. 무료 | 처음 쓰시는 대부분의 대표님 ⭐ |
| **Google Antigravity** | 차세대 AI IDE. Gemini + 클로드 코드 함께 사용. 무료 | 최신 실험을 좋아하시는 분 |
| **Cursor** | AI 내장 IDE. 월 $20 | 이미 쓰시던 분 |
| **JetBrains 계열** | 전문 개발자용. 유료 | 개발팀 운영하시는 분 |

### 옵션 A. VS Code (안전한 선택)

1. https://code.visualstudio.com/download 접속
2. **Windows** / **macOS** 클릭 → 다운로드
3. 설치 파일 실행 → 기본값으로 설치
4. 실행 후 내장 터미널 열기: `Terminal` → `New Terminal` (또는 `` Ctrl + ` ``)
5. 터미널에 `claude` 입력 → 연동 확인

**추천 확장**
- `Claude Code for VS Code` — 공식 확장. IDE 안에서 대화형 UI로 사용 가능
- `Korean Language Pack` — 메뉴 한글화

VS Code 왼쪽 사이드바 **Extensions 아이콘**(네모 4개) → 이름 검색 → Install.

### 옵션 B. Google Antigravity (최신·실험적)

Google이 2026년 초 공개한 Agent-First AI IDE. MCP·Skills 표준을 공유해 클로드 코드와 함께 쓸 수 있습니다.

1. https://antigravity.google 접속
2. 구글 계정으로 로그인
3. **Download for Windows** / **Download for macOS**
4. 설치 파일 실행 → 기본값으로 설치
5. 실행 후 구글 계정 연동 → Gemini 3 Pro/Flash 무료 사용 가능
6. 내장 터미널에서 `claude` 명령어 실행

> ⚠️ 현재 Antigravity는 안정성 이슈(컨텍스트 유실·에이전트 조기 종료 등)가 보고됩니다. **메인은 VS Code**로 쓰고 Antigravity는 실험용으로 두는 것을 권장합니다.

---

## ✅ 최종 동작 확인

모든 설치가 끝나면 순서대로 확인합니다.

- [ ] `git --version` → 버전 출력
- [ ] `claude --version` → 버전 출력
- [ ] `claude doctor` → 모두 OK
- [ ] `claude` 실행 후 `>` 프롬프트 출력
- [ ] `안녕하세요` 입력 → 한국어로 답변 수신
- [ ] IDE 실행됨 (VS Code 또는 Antigravity)
- [ ] IDE 내장 터미널에서 `claude` 실행됨

모두 ✅ 이면 **STEP 1 완료**입니다.

---

## 🚨 자주 발생하는 오류

**Q1. 설치 명령어가 "명령을 찾을 수 없습니다"로 실패**
- 원인: 터미널 종류(PowerShell/CMD) 혼동
- 해결: 프롬프트 앞 확인 — `PS C:\` 는 PowerShell, `C:\` 는 CMD

**Q2. `claude` 실행 시 로그인 브라우저가 안 열림**
- 해결: `claude login` 직접 실행
- 그래도 안 되면 방화벽·사내망 외부 OAuth 차단 확인

**Q3. Max 플랜 결제 실패**
- 원인: 국내 체크카드는 해외 결제가 거부되는 경우가 많음
- 해결: 해외 결제 가능한 신용카드 또는 PayPal 연결

**Q4. `claude` 실행 시 "Pro 이상 플랜이 필요합니다"**
- 원인: 로그인 계정이 Max 결제 계정과 다름
- 해결: `claude logout` → 다시 `claude login` → **결제한 계정으로** 로그인

**Q5. Windows에서 한글 깨짐**
- PowerShell에서 `chcp 65001` 입력
- 또는 Windows 터미널 설정 → 기본값 → **코드 페이지 65001 (UTF-8)**

**Q6. 회사 PC인데 관리자 권한이 없음**
- 네이티브 인스톨러는 관리자 권한 없이도 설치됩니다(홈 디렉터리에 설치)
- 회사 보안 정책이 스크립트 실행을 막으면 IT팀에 문의하셔야 합니다

---

## 🔄 업데이트 / 제거

### 업데이트
- 네이티브 설치는 자동으로 업데이트됩니다(백그라운드)
- 수동으로 즉시 업데이트: `claude update`

### 완전 제거 (Windows PowerShell)
```powershell
Remove-Item -Path "$env:USERPROFILE\.local\bin\claude.exe" -Force
Remove-Item -Path "$env:USERPROFILE\.local\share\claude" -Recurse -Force
Remove-Item -Path "$env:USERPROFILE\.claude" -Recurse -Force
Remove-Item -Path "$env:USERPROFILE\.claude.json" -Force
```

---

## 🍎 macOS 사용자 참고

대부분 Windows와 동일합니다. 차이점만 정리합니다.

| 항목 | macOS 방법 |
|------|-----------|
| Git | 이미 설치됨(`git --version`으로 확인). 없으면 `xcode-select --install` |
| Claude Code 설치 | `curl -fsSL https://claude.ai/install.sh \| bash` |
| Homebrew 사용자 | `brew install --cask claude-code` |
| 터미널 | **Terminal** 앱 또는 **iTerm2** |

---

## 📎 공식 참고

- Claude Code 공식 문서 — https://docs.claude.com
- Claude Max 플랜 — https://claude.com/pricing/max
- Git for Windows — https://git-scm.com/downloads/win
- Node.js — https://nodejs.org/en/download
- VS Code — https://code.visualstudio.com/download
- Google Antigravity — https://antigravity.google

---

**다음 단계** → `STEP2_설치-체크리스트.md` (설치 상태 빠른 진단)
