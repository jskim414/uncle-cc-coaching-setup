# 마크다운 에디터 구현 계획서

## 1. 프로젝트 개요

로컬 마크다운 문서를 웹에서 열어 읽고/편집하면서, 라인 단위로 메모(피드백)를 달아 한번에 내보낼 수 있는 경량 에디터.
AI가 생성한 초안 문서를 리뷰할 때, 위치별 피드백을 빠르게 모아 Claude Code에 전달하는 것이 핵심 사용 시나리오.

- 사용자: 1인 (개인 도구)
- 브라우저: Microsoft Edge (Chromium 기반, File System Access API 지원)
- 배포: Vercel 정적 호스팅

---

## 2. 기술 스택

| 영역 | 선택 | 근거 |
|------|------|------|
| 빌드/번들러 | **Vite** | 빠른 HMR, 정적 SPA 빌드에 최적 |
| UI 프레임워크 | **React 18** | 컴포넌트 기반 UI, 생태계 풍부 |
| 언어 | **TypeScript** | 타입 안정성 |
| 에디터 엔진 | **CodeMirror 6** | 경량, 라인 단위 이벤트/데코레이션 API 강력, 마크다운 모드 내장 |
| 상태관리 | **Zustand** | 보일러플레이트 최소, 가벼움 |
| 스타일링 | **Tailwind CSS** | B&W 디자인 빠르게 구현 |
| 배포 | **Vercel** | 프리티어, 정적 SPA 배포 |

---

## 3. 파일 접근 방식

**File System Access API** 사용 (Edge/Chrome 지원).

| 기능 | API | 비고 |
|------|-----|------|
| 파일 열기 | `showOpenFilePicker()` | 파일 탐색기 다이얼로그 |
| 파일 저장 | `FileSystemFileHandle.createWritable()` | 핸들 유지 시 원본에 직접 저장 |
| 다른 이름으로 저장 | `showSaveFilePicker()` | 핸들 없을 때 fallback |
| 드래그 앤 드롭 | `DataTransfer.files` | 보조 열기 수단 |

> 경로 붙여넣기로 직접 열기는 브라우저 보안 정책상 불가.
> 대신 (1) 파일 탐색기 열기, (2) 드래그 앤 드롭 두 가지 방식 지원.

---

## 4. 화면 레이아웃

```
┌─────────────────────────────────────────────────────────┐
│  [파일열기] [저장(Ctrl+S)] [메모 복사] [메모 저장]  파일명 │  ← 툴바
├────────────────────────┬────────────────────────────────┤
│                        │  메모 패널                      │
│   CodeMirror 6         │                                │
│   마크다운 에디터       │  ┌─ L12-14 ──────────────────┐ │
│                        │  │ > 원문 인용 (회색 배경)     │ │
│                        │  │ 사용자 메모 내용     [삭제] │ │
│                        │  └────────────────────────────┘ │
│   좌측 50%             │                                │
│                        │  ┌─ L28 ──────────────────────┐ │
│   - 라인 클릭 → 메모   │  │ > 원문 인용 (회색 배경)     │ │
│   - 드래그 → 범위 메모  │  │ 사용자 메모 내용     [삭제] │ │
│                        │  └────────────────────────────┘ │
│                        │               우측 50%          │
├────────────────────────┴────────────────────────────────┤
│  Line 12 | Col 5 | 메모 3개                              │
└─────────────────────────────────────────────────────────┘
```

- 기본 비율: 좌 50% / 우 50%
- 메모가 없을 때 우측 패널: 안내 텍스트 ("라인을 선택하고 메모를 추가하세요")

---

## 5. 핵심 기능 상세

### 5-1. 파일 열기/저장

- **열기**: 툴바 버튼 또는 `Ctrl+O` → `showOpenFilePicker({ types: [{ accept: { 'text/markdown': ['.md'] } }] })`
- **드래그 앤 드롭**: 에디터 영역에 `.md` 파일 드롭 → 내용 로드
- **저장**: `Ctrl+S` → 파일 핸들 있으면 덮어쓰기, 없으면 `showSaveFilePicker()`
- **새 파일**: 파일 로드 없이 바로 편집 시작 가능 (빈 에디터)

### 5-2. 라인 선택 → 메모 추가

**플로우:**
1. 에디터 거터(줄번호 영역) 클릭 → 해당 라인 선택 + 하이라이트
2. Shift+클릭 또는 거터 드래그 → 범위 선택
3. 선택 즉시 선택 영역 근처에 메모 입력 팝업 표시
4. 메모 입력 후 Enter(또는 확인 버튼) → 우측 패널에 메모 카드 추가
5. 에디터에서 해당 라인에 메모 표시 데코레이션 (예: 배경색 연한 표시)

**CodeMirror 6 활용:**
- `gutter` extension: 커스텀 거터 마커 (메모 있는 라인 표시)
- `Decoration.line()`: 메모가 달린 라인 배경색
- `EditorView.domEventHandlers`: 거터 클릭 이벤트 핸들링
- `StateField`: 선택된 라인 범위 상태 관리

### 5-3. 메모 팝업

- 위치: 선택된 라인 바로 우측 또는 상단에 플로팅
- 구성: textarea (2~3줄) + 확인/취소 버튼
- 단축키: `Enter` → 확인 (Shift+Enter → 줄바꿈), `Esc` → 취소
- 포커스: 팝업 열리면 자동 포커스

### 5-4. 메모 패널 (우측)

- 메모 카드 정렬: 라인 번호 오름차순
- 각 카드 구성:
  - 헤더: `L{시작}-{끝}` 라인 번호 뱃지
  - 인용 블록: 해당 라인 원문 (회색 배경, 읽기 전용)
  - 메모 본문: 편집 가능한 텍스트 영역
  - 삭제 버튼: 개별 메모 삭제
- 카드 클릭 → 좌측 에디터에서 해당 라인으로 스크롤 + 하이라이트
- 메모 없을 때: 빈 상태 안내 UI

### 5-5. 메모 내보내기

**두 가지 방식:**
1. **클립보드 복사**: 툴바 버튼 → 마크다운 형식으로 클립보드에 복사
2. **파일 저장**: 툴바 버튼 → `showSaveFilePicker()`로 `.md` 파일 저장

**내보내기 형식:**
```markdown
# 문서 리뷰 메모: {파일명}
날짜: {YYYY-MM-DD}

## Line 12-14
> 원문 인용 텍스트...

사용자 메모 내용

## Line 28
> 원문 인용 텍스트...

사용자 메모 내용
```

---

## 6. 데이터 모델

```typescript
// 메모 하나
interface Memo {
  id: string;              // 고유 ID (nanoid 등)
  lineFrom: number;        // 시작 라인 (1-based)
  lineTo: number;          // 끝 라인 (1-based, 단일 라인이면 lineFrom과 동일)
  quotedText: string;      // 해당 라인 원문 스냅샷
  content: string;         // 사용자 메모 내용
  createdAt: number;       // timestamp
}

// 에디터 상태
interface EditorState {
  fileName: string | null;
  fileHandle: FileSystemFileHandle | null;  // 저장용 핸들
  content: string;
  isDirty: boolean;        // 미저장 변경 여부
}

// 메모 상태
interface MemoState {
  memos: Memo[];
  selectedMemoId: string | null;
}
```

- 메모 데이터는 세션 메모리에만 유지 (새로고침 시 초기화)
- 문서 내용이 변경되면 기존 메모의 라인 번호는 자동 갱신하지 않음 (리뷰 시나리오에서 문서 대폭 수정은 드묾)

---

## 7. 프로젝트 파일 구조

```
markdown-editor/
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.ts
├── postcss.config.js
├── src/
│   ├── main.tsx                 # 엔트리포인트
│   ├── App.tsx                  # 루트 레이아웃 (좌/우 분할)
│   ├── stores/
│   │   ├── editorStore.ts       # 문서 상태, 파일 핸들
│   │   └── memoStore.ts         # 메모 CRUD
│   ├── components/
│   │   ├── Toolbar.tsx          # 상단 툴바 (열기/저장/내보내기)
│   │   ├── Editor.tsx           # CodeMirror 6 래퍼
│   │   ├── MemoPanel.tsx        # 우측 메모 패널 컨테이너
│   │   ├── MemoCard.tsx         # 개별 메모 카드
│   │   ├── MemoPopup.tsx        # 라인 선택 시 메모 입력 팝업
│   │   └── StatusBar.tsx        # 하단 상태바
│   ├── lib/
│   │   ├── fileAccess.ts        # File System Access API 래퍼
│   │   ├── exportMemo.ts        # 메모 → 마크다운 변환 + 복사/저장
│   │   └── editorExtensions.ts  # CodeMirror 커스텀 확장 (거터, 데코레이션)
│   └── styles/
│       └── globals.css          # Tailwind 디렉티브 + B&W 커스텀
└── public/
    └── favicon.svg
```

---

## 8. 디자인 원칙

- **컬러**: White (#FFFFFF) 배경, Black (#000000) 텍스트, Gray (#F5F5F5, #E5E5E5, #666) 보조
- **폰트**: 에디터 — 모노스페이스 (시스템 기본), UI — 시스템 산세리프
- **보더**: 1px solid #E5E5E5, 라운드 최소 (4px)
- **메모 카드**: 하얀 배경 + 얇은 보더 + 미세 그림자
- **하이라이트**: 메모 달린 라인 배경 연한 회색 (#F9F9F9)
- **인터랙션**: hover 시 배경색 변화, 전환 150ms

---

## 9. 단축키

| 단축키 | 동작 |
|--------|------|
| `Ctrl+O` | 파일 열기 |
| `Ctrl+S` | 파일 저장 |
| `Ctrl+Shift+C` | 메모 전체 클립보드 복사 |
| `Esc` | 메모 팝업 닫기 |
| `Enter` | 메모 팝업 내 확인 (메모 추가) |
| `Shift+Enter` | 메모 팝업 내 줄바꿈 |

---

## 10. 구현 단계

| 단계 | 내용 | 산출물 |
|------|------|--------|
| **Phase 1** | 프로젝트 초기화 (Vite+React+TS+Tailwind), 기본 레이아웃 (좌/우 50:50 분할) | 빈 껍데기 앱 |
| **Phase 2** | CodeMirror 6 통합 — 마크다운 syntax highlighting, 기본 편집 | 작동하는 에디터 |
| **Phase 3** | File System Access API — 파일 열기/저장/드래그앤드롭 | 로컬 파일 읽기/쓰기 |
| **Phase 4** | 라인 선택 — 거터 클릭/드래그, 라인 하이라이트, 메모 팝업 UI | 라인 선택 + 팝업 |
| **Phase 5** | 메모 시스템 — 메모 Store, 메모 패널, 카드 렌더링, 에디터 연동 | 메모 CRUD + 양방향 연동 |
| **Phase 6** | 메모 내보내기 — 클립보드 복사 + 파일 저장 | 내보내기 기능 |
| **Phase 7** | 디자인 마무리 — B&W 테마, 단축키, 빈 상태 UI, 미저장 경고 | 완성된 UI |
| **Phase 8** | Vercel 배포 — 빌드 설정, 배포 테스트 | 라이브 URL |

---

## 11. 제약 사항 / 미구현

- AI/LLM 연동 없음
- 마크다운 미리보기(프리뷰) 없음
- 다중 파일 탭 없음 (한 번에 하나의 문서)
- 메모 영속성 없음 (새로고침 시 초기화)
- Firefox/Safari 미지원 (File System Access API 미지원)
- 문서 편집 시 메모 라인 번호 자동 추적 미지원 (리뷰 시나리오 기준 불필요)
