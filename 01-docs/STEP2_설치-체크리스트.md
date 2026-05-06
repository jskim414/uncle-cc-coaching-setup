# STEP 2. 필수 프로그램 설치 체크리스트

> 내 PC에 뭐가 깔려 있고 뭐가 없는지 **5분 안에** 확인합니다.
> 대상: 비개발자 대표님 / Windows 10·11 기본, macOS 병기

---

## 🎯 이 문서를 쓰는 방법

1. 터미널을 엽니다 (아래 "0. 터미널 여는 법" 참고)
2. 각 항목의 **확인 명령어**를 복사·붙여넣기 → Enter
3. **✅ 성공 / ❌ 실패** 체크
4. ❌ 난 항목만 **설치 방법**으로 이동해서 설치합니다

막히는 부분이 있으면 결과 화면을 캡처해 공유해 주세요.

---

## 0. 터미널 여는 법

### Windows

**방법 A. PowerShell (권장)**
- 키보드 `Windows + X` → **터미널** 또는 **Windows PowerShell**
- 프롬프트가 `PS C:\Users\...>` 로 보이면 PowerShell입니다.

**방법 B. 명령 프롬프트(CMD)**
- `Windows + R` → `cmd` 입력 → Enter
- 프롬프트가 `C:\Users\...>` 로 보이면 CMD입니다.

> ⚠️ PowerShell과 CMD는 명령어 문법이 다릅니다. 아래 명령어는 둘 다에서 작동하는 것만 사용했습니다.

### macOS
- `Cmd + Space` → "터미널" 검색 → 실행

---

## 📋 필수 프로그램 진단표

| 번호 | 프로그램 | 필수 여부 | 용도 |
|------|---------|----------|------|
| ① | **Git** | 🔴 필수(Windows) | 클로드 코드가 내부적으로 쓰는 Bash 셸 |
| ② | **Node.js / npm** | 🟡 선택 | MCP 서버·확장 도구 실습 |
| ③ | **Python / pip** | 🟡 선택 | 파이썬 기반 자동화 실습 |
| ④ | **Claude Code** | 🔴 필수 | AI 코딩 에이전트 본체 |
| ⑤ | **IDE**(VS Code / Antigravity) | 🟢 강력 권장 | 코드를 보면서 편집하는 창 |
| ⑥ | **Claude Max 플랜** | 🔴 필수 | 클로드 코드 사용 권한 |

---

## ① Git

### 확인 명령어
```bash
git --version
```

### 결과 해석
- ✅ `git version 2.xx.x` 형태로 출력
- ❌ "명령을 찾을 수 없습니다" 계열 오류

### [ ] 설치됨  /  [ ] 미설치

### ❌ 일 때 설치 방법

**Windows**
1. https://git-scm.com/downloads/win 접속
2. **64-bit Git for Windows Setup** 다운로드
3. `.exe` 실행 → 옵션 **모두 기본값 Next** → Install
4. **터미널을 새로 열고** 다시 `git --version` 확인

**macOS**
- 대부분 이미 설치되어 있습니다. 없으면 터미널에서 `xcode-select --install` 실행.

---

## ② Node.js / npm

### 확인 명령어
```bash
node --version
npm --version
```

### 결과 해석
- ✅ `v20.x.x`, `10.x.x` 같은 숫자
- ❌ "명령을 찾을 수 없습니다"
- ⚠️ `v16.x.x` 이하면 업데이트 권장

### [ ] 설치됨 (버전: ______ )  /  [ ] 미설치

### ❌ 일 때 설치 방법
1. https://nodejs.org/en/download 접속
2. **LTS 버전**(20.x 이상) 다운로드
3. 설치 파일 실행 → 기본값으로 Next
4. 터미널을 새로 열고 다시 확인

> 💡 클로드 코드 네이티브 설치만 쓸 거면 Node.js 없이도 됩니다. 다만 MCP·npm 기반 확장 실습에 필요하므로 설치를 권장합니다.

---

## ③ Python / pip

### 확인 명령어

**Windows**
```bash
python --version
pip --version
```

**macOS / Linux**
```bash
python3 --version
pip3 --version
```

### 결과 해석
- ✅ `Python 3.11.x` / `pip 24.x.x` 같은 숫자
- ❌ Windows에서 `python` 입력 시 Microsoft Store가 열리면 미설치 상태
- ⚠️ `Python 2.x` 는 업그레이드 필요

### [ ] 설치됨 (버전: ______ )  /  [ ] 미설치

### ❌ 일 때 설치 방법

**Windows**
1. https://www.python.org/downloads/ 접속
2. **Download Python 3.1x.x** 클릭
3. 설치 화면에서 **반드시 체크**:
   - ☑ **Add Python to PATH**
   - ☑ Install launcher for all users
4. **Install Now**
5. 터미널 새로 열고 `python --version` 확인

**macOS**
- `python3 --version` 으로 이미 설치됨을 확인. 없으면 https://www.python.org/downloads/macos/ 또는 `brew install python@3.12`

> 💡 Python은 필수가 아닙니다. 자동화·데이터 처리 실습이 필요할 때 설치하세요.

---

## ④ Claude Code

### 확인 명령어
```bash
claude --version
```

### 결과 해석
- ✅ `2.1.xxx` 같은 버전 출력
- ❌ "명령을 찾을 수 없습니다"

### 설치되어 있다면 정상 진단
```bash
claude doctor
```
- 모든 항목 OK 이면 정상

### [ ] 설치됨 (버전: ______ )  /  [ ] 미설치

### ❌ 일 때 설치 방법 (네이티브 인스톨러 권장)

**Windows — PowerShell**
```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows — CMD**
```bash
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

**macOS / Linux / WSL**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

> ⚠️ `'irm' is not recognized` 오류가 나면 CMD에서 PowerShell 명령을 쓴 것입니다. 프롬프트 앞이 `PS C:\` 면 PowerShell, `C:\` 면 CMD 입니다.

### 설치 후 최초 실행·인증
1. 아무 폴더에서 터미널 열기
2. `claude` 입력 → Enter
3. 브라우저가 자동으로 열림 → **Max 플랜 계정으로 로그인**
4. "Authorize Claude Code" 클릭 → 터미널 복귀
5. `>` 프롬프트가 뜨면 성공

---

## ⑤ IDE

시작 메뉴(Windows) 또는 Launchpad(macOS)에서 아이콘을 찾으면 설치된 것입니다.

| IDE | 추천도 |
|------|-------|
| **VS Code** | ⭐⭐⭐ 안정·표준 |
| **Google Antigravity** | ⭐⭐ 최신·실험적 |
| **Cursor** | ⭐ 이미 쓰시던 분만 |

### [ ] 하나 이상 설치됨  /  [ ] 전부 미설치

### ❌ 일 때 설치 방법

#### VS Code (처음이라면 이쪽)
1. https://code.visualstudio.com/download 접속
2. Windows / macOS 클릭 → 다운로드
3. 설치 파일 실행 → 기본값으로 Next
4. 실행 후 내장 터미널 열기: `Terminal` → `New Terminal` (또는 `` Ctrl + ` ``)
5. 터미널에 `claude` 입력 → 연동 확인

**추천 확장**
- `Claude Code for VS Code` — 공식 확장
- `Korean Language Pack for Visual Studio Code` — 메뉴 한글화

#### Google Antigravity
1. https://antigravity.google 접속
2. 구글 계정으로 로그인
3. Download for Windows / macOS
4. 설치 → 실행 → Gemini 무료 연동
5. 내장 터미널에서 `claude` 실행

> ⚠️ 현재 Antigravity는 안정성 이슈가 보고됩니다. 메인은 VS Code, 실험용으로 Antigravity를 권장합니다.

---

## ⑥ Claude Max 플랜

### 확인 방법
1. https://claude.ai 접속 → 로그인
2. 왼쪽 하단 프로필 → **Settings** → **Plans & billing**
3. 현재 플랜 확인

### 플랜별 실습 가능 여부
| 플랜 | 월 요금 | 실습 |
|------|--------|------|
| Free | $0 | ❌ 클로드 코드 사용 불가 |
| Pro | $20 | ⚠️ 한도로 자주 멈춤 |
| **Max 5x** | **$100** | ✅ **대부분 대표님 권장** |
| Max 20x | $200 | ✅ 하루 종일 쓰시는 분 |

### [ ] Max 5x 이상  /  [ ] Pro 이하 (업그레이드 필요)

### ❌ 일 때 가입 방법
1. https://claude.com/pricing 접속
2. **Max 5x** 선택 → 카드 결제
3. **해외 결제 가능 신용카드** 사용 (국내 체크카드는 실패가 많습니다)

> 💡 과외 이후 부담되면 바로 다운그레이드 가능합니다(한 달 단위 과금).

---

## ✅ 최종 동작 확인

모든 설치가 끝난 뒤 순서대로 확인합니다.

- [ ] `git --version` → 버전 출력
- [ ] `node --version` → 버전 출력 (선택)
- [ ] `python --version` → 버전 출력 (선택)
- [ ] `claude --version` → 버전 출력
- [ ] `claude doctor` → 모두 OK
- [ ] `claude` 실행 후 `>` 프롬프트
- [ ] `안녕하세요` 입력 → 한국어 답변 수신
- [ ] IDE 실행됨 (VS Code 또는 Antigravity)
- [ ] IDE 내장 터미널에서 `claude` 실행됨
- [ ] Claude Max 5x 이상 플랜 활성화

모두 ✅ 이면 **STEP 2 완료**입니다.

---

## 🚨 자주 발생하는 오류 TOP 5

**Q1. "명령을 찾을 수 없습니다"**
- 원인: 터미널 종류(PowerShell/CMD) 혼동
- 해결: 프롬프트 앞 확인 — `PS C:\` 는 PowerShell, `C:\` 는 CMD

**Q2. `python` 쳤더니 Microsoft Store가 열림**
- 원인: Python 미설치 (Windows 기본 동작)
- 해결: python.org 다운로드 시 **"Add Python to PATH"** 체크

**Q3. `claude` 실행해도 로그인 브라우저가 안 열림**
- 해결: `claude login` 직접 실행
- 그래도 안 되면 방화벽·사내망 외부 OAuth 차단 확인

**Q4. 결제한 계정인데 "Pro 이상 플랜이 필요합니다" 오류**
- 원인: 로그인 계정이 Max 결제 계정과 다름
- 해결: `claude logout` → 다시 `claude login` → **결제 계정으로** 로그인

**Q5. Windows 터미널 한글 깨짐**
- 해결: PowerShell에서 `chcp 65001` 입력
- 또는 Windows 터미널 설정 → 기본값 → **코드 페이지 65001 (UTF-8)**

---

## 📎 공식 참고

- Claude Code 공식 문서 — https://docs.claude.com
- Claude Max 플랜 — https://claude.com/pricing/max
- Git for Windows — https://git-scm.com/downloads/win
- Node.js LTS — https://nodejs.org/en/download
- Python — https://www.python.org/downloads/
- VS Code — https://code.visualstudio.com/download
- Google Antigravity — https://antigravity.google

---

**다음 단계** → `STEP3_기초-상식.md`
