# 클코형 클로드 코드 노하우 정리

Last updated on 2026-04-25

## 사전 준비

- 클로드 코드를 제대로 활용하기 위해 사전에 설치 및 준비해야 할 것
  - git bash 설치 
    - https://git-scm.com/install/windows
    - 이후 github 로그인 연동
  - node.js 설치
    - https://nodejs.org/ko/download
  - python 설치
    - https://www.python.org/downloads/
  - IDE 설치
    - Antigravity : https://antigravity.google/
  - 클로드 유료 요금제 활용
    - Max100 이상 권장
- 클로드 코드 설치
  - Windows 11 기준,
  - Powershell을 관리자 권한으로 실행한 후 설치
  - https://code.claude.com/docs/ko/quickstart

## 왜 클로드 코드인가?

### 일반 LLM 웹서비스 대비 차이
 - 높은 자유도 : 사용자의 입맛대로 프로젝트 폴더를 구분해서 폴더 구조, 파일 관리 등을 자유롭게 세팅할 수 있음.
 - 확장성 : 각 프로젝트마다 알맞은 스킬(SKILL), 에이전트, MCP, 플러그인 등 접목해서 나만의 작업 환경 세팅
 - 축적 및 자기 강화 : 산출물이 계속 축적되는 구조. 이후 산출물을 재학습해서 프로젝트 내 작업지침을 자기 강화할 수 있음

### Cowork 대비 차이
 - 아무래도 클로드 코워크(Cowork)는 클로드 PC앱에서 작동하기 때문에 자유도나 확장성이 낮음
 - PC 하나 당 하나의 앱만 사용 (병렬적으로 프로젝트 수행 불가한 것으로 알고 있음)

## 시작하기
 
### 기본 시작
 1. IDE 실행 (Antigravity)
 2. 프로젝트 폴더 열기
 3. 클로드 코드 실행
  - 기본 명령어 : claude --dangerously-skip-permissions

### 기본 팁
 - 주요 단축키
  - Ctrl + J : 터미널 창 보이기, 숨기기
  - Alt + Enter : (터미널 명령어 입력창에서) 명령어 줄바꿈
  - Ctrl + O : 터미널로 작업 실행했을 때 구체적인 작업 진행현황 보여줌
- 기본 사용법
  - 터미널로 폴더 내 특정 파일 입력
    - 파일을 마우스 좌클릭하고 드래그해서 터미널로 끌어 당기기
    - 또는 터미널 명령어 입력창에서 @누르고 참조
  - 프로젝트 폴더 외 폴더 추가하기 
    - 폴더 있는 곳에 마우스 우클릭 'Add folder to project' 클릭하면 기존 폴더 외에 새로운 폴더 추가
    - 프로젝트 폴더 외 외부 폴더 참조할 때 활용
  - 모든 문서는 마크다운(md파일)으로 작성하고 관리한다
  - /model
    - 현재 모델 확인 및 선택
    - 기본적으로 왠만하면 OPUS 사용하기 (기획,계획,분석 등)
      - 단순 업무는 SONNET 사용 (리서치 계획에 맞춰서 실행, 개발 구현 등)

### 기본 멘탈 모델
- AI가 사람보다 더 잘 안다. 모르는 것이 있으면, 막히는 것이 있으면 AI에게 물어보고 한다. 
  - 우리의 역할은 AI가 나의 의도를 명료하게 이해할 수 있도록 맥락을 최대한 투입, 정리해서 제공하는 것이다
- 1차 계획도 AI가 한다. 다만 우리는 사전에 맥락을 제공한다
  - 웹앱 : 사전 앱 목적, 기획 기초 작성해서 먼저 전달
  - 리서치 : 사전 리서치 개요 작성해서 먼저 전달

### 초기 입문 실습

1. 리서치
2. 간단한 웹앱(토이프로젝트) 만들기
  - HTML 기반
3. 배포해보기
  - HTML 기반 간단한 서비스 : Netlify 활용
  - Vite 또는 DB 등 다소 기술 들어간 서비스 : Vercel, Railways 활용
4. 나만의 에이전트 만들기
  - 에이전트 정의 : 내가 해야 할 어떤 일을 대신하는 존재, 도구
  - 기본 접근
    - input -> process -> output 구조
    - 먼저 input과 output 샘플 준비
    - input을 넣으면 output이 나오게 하는 작업지시서 생성 요청
    - 생성된 작업지시서가 일종의 에이전트
      - input 드래그
      - 작업 지시서 드래그
      - 작업 지시서 참고해서 산출물 만들어줘라고 명령하기
5. 초기 참조할 만한 클코형 영상
  - 영문 아티클 정제 + 오피스 문서 마크다운 변환 : https://cafe.naver.com/unclecc/6
  - 심층 리서치 : https://cafe.naver.com/unclecc/26
  - 클로드 for 엑셀 : https://cafe.naver.com/unclecc/30
  - 랜딩페이지 만들기 : https://cafe.naver.com/unclecc/41
        
## 앞으로 해야 할 것

### 추가적으로 알아야 할 개념

- **CLAUDE.md**
  - 프로젝트 루트에 두는 작업 지침서. 클로드 코드가 매 대화 시작 시 자동으로 읽어 들이는 "프로젝트 사용 설명서"
  - 코딩 컨벤션, 도구 사용 규칙, 자주 쓰는 명령어, 금지 사항 등을 적어두면 매번 설명할 필요 없음
  - 참조: https://docs.claude.com/en/docs/claude-code/memory

- **SKILL (스킬)**
  - 특정 작업을 수행하는 방법을 모아둔 "전문 매뉴얼". 필요할 때 클로드가 알아서 불러와 사용
  - 예: PDF 처리, 엑셀 작업, 슬라이드 생성 등 반복 작업을 표준화
  - 참조: https://docs.claude.com/en/docs/claude-code/skills

- **AGENT (에이전트)**
  - 특정 역할을 가진 "전문 비서". 메인 클로드가 작업을 위임해서 병렬로 처리 가능
  - 컨텍스트를 분리해서 사용하므로 메인 대화창이 깔끔하게 유지됨
  - 참조: https://docs.claude.com/en/docs/claude-code/sub-agents

- **MCP (Model Context Protocol)**
  - 클로드를 외부 도구·서비스에 연결하는 표준 규격. 일종의 "USB 포트"
  - 예: Notion, Google Drive, Gmail, GitHub, 브라우저 자동화 등을 클로드와 연동
  - 참조: https://docs.claude.com/en/docs/claude-code/mcp

- **플러그인 (Plugin)**
  - 슬래시 커맨드, 에이전트, MCP, 스킬을 한 묶음으로 패키징해서 배포·설치하는 단위
  - 다른 사람이 만든 워크플로우를 통째로 가져와 쓸 수 있음
  - 참조: https://docs.claude.com/en/docs/claude-code/plugins

- **API (외부 API 활용)**
  - 외부 서비스의 기능·데이터를 코드로 가져와 쓸 수 있게 해주는 "출입문". 클로드 코드에 API 키만 등록하면 해당 서비스를 자동으로 활용 가능
  - 예: OpenAI(이미지 생성), Google(검색·지도), 네이버(검색·번역), 공공데이터포털, 노션·슬랙 등 — 대부분의 SaaS가 API를 제공
  - MCP가 미리 만들어진 "완제품 연결 도구"라면, API는 직접 조립해서 원하는 기능만 뽑아 쓰는 방식
  - 참조: https://replicate.com/
