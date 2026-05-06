# 엑셀 플로팅 위젯 — 최종 구현 가이드

> 엑셀(.xlsx) 파일을 Always-on-Top 플로팅 위젯으로 띄워서, 셀 클릭만으로 바로 복사할 수 있는 Windows 데스크톱 도구.
> 문서 작업 중 엑셀 데이터를 반복 복사·붙여넣기할 때, 창을 왔다갔다하지 않고 위젯에서 바로 가져다 쓸 수 있다.

---

## 1. 이런 사람을 위한 도구

- hwpx, docx 문서 작업을 하면서 엑셀에 있는 계좌번호, 은행명, 주민등록번호 등을 **반복적으로 복사·붙여넣기**하는 직장인
- 프로그램 창을 왔다갔다하는 게 번거로운 사람

---

## 2. 주요 기능

| 기능 | 설명 |
|------|------|
| 파일 열기 | .xlsx 파일 선택, 시트 전환 드롭다운 |
| Always on Top | 어떤 프로그램 위에든 항상 떠 있음 (체크박스로 on/off) |
| 셀 클릭 복사 | 셀을 클릭하면 해당 값이 즉시 클립보드에 복사됨 |
| 영역 드래그 복사 | 마우스 드래그로 여러 셀 선택 → 탭/줄바꿈 구분으로 복사 (엑셀/한글에 표 형태 유지) |
| 복사 모드 전환 | 자동복사(클릭=복사) ↔ 수동복사(선택 후 Ctrl+C) 전환 가능 |
| 검색/필터 | 실시간 검색, 매칭 셀 노란색 하이라이트, 결과 건수 표시 |
| 투명도 조절 | 슬라이더로 30~100% 조절 (문서 작업 방해 최소화) |
| 새로고침 | 엑셀 파일이 외부에서 변경되었을 때 다시 읽기 |
| 복사 토스트 | 복사 완료 시 하단에 "복사: 값" 알림 표시 (1.2초 후 사라짐) |

---

## 3. 기술 스택

| 항목 | 선택 | 이유 |
|------|------|------|
| 언어 | **Python 3.12+** | 빠른 개발, 풍부한 생태계 |
| UI | **tkinter** | Python 내장, 별도 설치 불필요, 가볍고 Always-on-Top 지원 |
| 엑셀 읽기 | **openpyxl** | .xlsx 표준 라이브러리, read_only 모드로 대용량 지원 |
| 클립보드 | **pyperclip** | 간단한 API, Windows/Mac/Linux 호환 |
| 배포 | **PyInstaller** | 단일 .exe 생성, 비개발자도 더블클릭으로 실행 |

---

## 4. UI 레이아웃

```
┌──────────────────────────────────────────┐
│  [파일 열기]  시트: [Sheet1 ▼]  [↻]      │  ← 1행: 파일 & 시트
├──────────────────────────────────────────┤
│  검색: [_______________] [✕]  3/5건      │  ← 2행: 검색
├──────────────────────────────────────────┤
│  투명도        자동복사     고정           │  ← 3행: 설정 라벨
│  [====●====]     ☑          ☑            │  ← 3행: 설정 컨트롤
├──────┬──────┬──────┬─────────────────────┤
│ 이름 │ 은행 │ 계좌 │ 이메일              │  ← 헤더 (파란 배경)
├──────┼──────┼──────┼─────────────────────┤
│홍길동│ 국민 │ 1234 │ hong@test.com       │  ← 클릭하면 복사
│김철수│ 신한 │ 5678 │ kim@test.com        │     드래그하면 영역 복사
│  ... │      │      │                     │
└──────┴──────┴──────┴─────────────────────┘
              💬 "계좌번호 복사됨!"          ← 토스트 알림
```

---

## 5. 프로젝트 폴더 구조

```
excel-widget/
├── main.py                 # 엔트리포인트
├── requirements.txt        # 의존성 목록
├── build.bat               # PyInstaller 빌드 스크립트
├── core/
│   ├── __init__.py
│   ├── excel_reader.py     # 엑셀 파일 읽기
│   └── clipboard.py        # 클립보드 복사
├── widget/
│   ├── __init__.py
│   ├── app.py              # 메인 윈도우 (모든 컴포넌트 조합)
│   ├── table.py            # 테이블 뷰 (Canvas 기반, 가상 렌더링)
│   ├── toolbar.py          # 상단 컨트롤바
│   └── toast.py            # 복사 피드백 토스트
└── assets/
    └── icon.ico            # 앱 아이콘 (선택)
```

---

## 6. 각 파일별 역할과 핵심 로직

### 6-1. `main.py` — 엔트리포인트

```python
"""엑셀 플로팅 위젯 — 엔트리포인트."""
import sys, os

# PyInstaller 빌드 시 경로 보정
if getattr(sys, "frozen", False):
    os.chdir(os.path.dirname(sys.executable))

from widget.app import ExcelWidgetApp

def main():
    app = ExcelWidgetApp()
    # 커맨드라인으로 파일 경로를 받으면 바로 열기
    if len(sys.argv) > 1:
        filepath = sys.argv[1]
        if os.path.isfile(filepath):
            app._load_file(filepath)
    app.run()

if __name__ == "__main__":
    main()
```

**포인트:**
- `sys.frozen` 체크: PyInstaller로 빌드된 .exe에서 실행 경로가 임시 폴더가 되므로 보정
- 커맨드라인 인자: `python main.py data.xlsx` 또는 .exe에 파일을 드래그&드롭하면 바로 열림

---

### 6-2. `core/excel_reader.py` — 엑셀 파일 읽기

```python
"""Excel (.xlsx) 파일 읽기 모듈."""
from openpyxl import load_workbook

class ExcelReader:
    def __init__(self, filepath: str):
        self.filepath = filepath
        self.wb = load_workbook(filepath, read_only=True, data_only=True)

    @property
    def sheet_names(self) -> list[str]:
        return self.wb.sheetnames

    def read_sheet(self, sheet_name: str) -> tuple[list[str], list[list[str]]]:
        """시트를 읽어 (헤더, 행 데이터) 반환. 모든 값은 문자열로 변환."""
        ws = self.wb[sheet_name]
        rows = []
        for row in ws.iter_rows():
            rows.append([self._cell_to_str(cell.value) for cell in row])
        if not rows:
            return [], []
        headers = rows[0]
        data = rows[1:]
        return headers, data

    def close(self):
        self.wb.close()

    @staticmethod
    def _cell_to_str(value) -> str:
        if value is None:
            return ""
        return str(value)
```

**포인트:**
- `read_only=True`: 메모리를 적게 쓰고 대용량 파일도 빠르게 읽음
- `data_only=True`: 수식 셀에서 계산된 값을 가져옴 (수식 자체가 아닌 결과값)
- 모든 셀 값을 문자열로 통일하여 UI 처리를 단순화

---

### 6-3. `core/clipboard.py` — 클립보드 복사

```python
"""클립보드 복사 유틸."""
import pyperclip

def copy_text(text: str):
    """단일 텍스트를 클립보드에 복사."""
    pyperclip.copy(text)

def copy_cells(values: list[list[str]]):
    """2D 셀 값을 탭/줄바꿈으로 구분하여 복사.
    엑셀/한글에 붙여넣기 시 표 형태로 유지됨."""
    text = "\n".join("\t".join(row) for row in values)
    pyperclip.copy(text)
```

**포인트:**
- `copy_cells`: 여러 셀을 `탭(\t)`으로 열 구분, `줄바꿈(\n)`으로 행 구분 → 엑셀이나 한글에 붙여넣기 하면 표 형태가 유지됨

---

### 6-4. `widget/toast.py` — 토스트 알림

```python
"""복사 완료 토스트 알림."""
import tkinter as tk

class Toast:
    def __init__(self, parent: tk.Widget):
        self.parent = parent
        self.label = None
        self._after_id = None

    def show(self, message: str, duration_ms: int = 1200):
        if self.label:
            self.label.destroy()
        if self._after_id:
            self.parent.after_cancel(self._after_id)

        self.label = tk.Label(
            self.parent, text=message,
            bg="#323232", fg="white", font=("맑은 고딕", 9),
            padx=12, pady=4,
        )
        self.label.place(relx=0.5, rely=1.0, anchor="s", y=-8)
        self._after_id = self.parent.after(duration_ms, self._hide)

    def _hide(self):
        if self.label:
            self.label.destroy()
            self.label = None
        self._after_id = None
```

**포인트:**
- `place(relx=0.5, rely=1.0)`: 부모 위젯의 하단 중앙에 절대 위치로 배치
- 기존 토스트가 있으면 교체 (중복 방지)
- `after()`로 1.2초 후 자동 제거

---

### 6-5. `widget/toolbar.py` — 상단 컨트롤바

```python
"""상단 컨트롤바: 파일/시트, 검색, 설정(투명도·복사모드·고정)."""
import tkinter as tk
from tkinter import ttk

FONT = ("맑은 고딕", 9)
LABEL_FONT = ("맑은 고딕", 8)
BG = "#F0F0F0"

class Toolbar(tk.Frame):
    def __init__(self, parent, **kwargs):
        super().__init__(parent, bg=BG, **kwargs)

        # 콜백 — app.py에서 연결
        self.on_sheet_change = None
        self.on_search = None
        self.on_topmost_toggle = None
        self.on_opacity_change = None
        self.on_file_open = None
        self.on_refresh = None
        self.on_autocopy_toggle = None

        # ── 1행: 파일 & 시트 ──
        row1 = tk.Frame(self, bg=BG)
        row1.pack(fill="x", padx=6, pady=(4, 0))

        self.btn_open = tk.Button(row1, text="파일 열기", font=FONT,
                                  command=self._on_file_open)
        self.btn_open.pack(side="left")

        tk.Label(row1, text="  시트:", bg=BG, font=FONT).pack(side="left")
        self.sheet_var = tk.StringVar()
        self.sheet_combo = ttk.Combobox(row1, textvariable=self.sheet_var,
                                        state="readonly", width=15, font=FONT)
        self.sheet_combo.pack(side="left", padx=(2, 0))
        self.sheet_combo.bind("<<ComboboxSelected>>", self._on_sheet_select)

        self.btn_refresh = tk.Button(row1, text="↻", font=("맑은 고딕", 10),
                                     command=self._on_refresh, width=2)
        self.btn_refresh.pack(side="left", padx=(6, 0))

        # ── 2행: 검색 ──
        row2 = tk.Frame(self, bg=BG)
        row2.pack(fill="x", padx=6, pady=(2, 0))

        tk.Label(row2, text="검색:", bg=BG, font=FONT).pack(side="left")
        self.search_var = tk.StringVar()
        self.search_entry = tk.Entry(row2, textvariable=self.search_var,
                                      font=FONT, width=25)
        self.search_entry.pack(side="left", padx=(4, 0), fill="x", expand=True)
        self.search_var.trace_add("write", self._on_search_change)

        self.btn_clear = tk.Button(row2, text="✕", font=("맑은 고딕", 8),
                                    command=self._clear_search, width=2)
        self.btn_clear.pack(side="left", padx=(2, 0))

        self.result_label = tk.Label(row2, text="", bg=BG,
                                      font=("맑은 고딕", 8), fg="#666666")
        self.result_label.pack(side="left", padx=(6, 0))

        # ── 3행: 설정 (라벨 줄 + 컨트롤 줄) ──
        settings = tk.Frame(self, bg=BG)
        settings.pack(fill="x", padx=6, pady=(4, 4))

        label_row = tk.Frame(settings, bg=BG)
        label_row.pack(fill="x")
        tk.Label(label_row, text="투명도", bg=BG, font=LABEL_FONT,
                 fg="#555555").pack(side="left")
        tk.Frame(label_row, width=50, bg=BG).pack(side="left")
        tk.Label(label_row, text="자동복사", bg=BG, font=LABEL_FONT,
                 fg="#555555").pack(side="left", padx=(8, 0))
        tk.Label(label_row, text="고정", bg=BG, font=LABEL_FONT,
                 fg="#555555").pack(side="left", padx=(16, 0))

        ctrl_row = tk.Frame(settings, bg=BG)
        ctrl_row.pack(fill="x", pady=(1, 0))

        self.opacity_scale = tk.Scale(ctrl_row, from_=30, to=100,
                                       orient="horizontal", length=90,
                                       showvalue=False, bg=BG,
                                       command=self._on_opacity)
        self.opacity_scale.set(100)
        self.opacity_scale.pack(side="left")

        self.autocopy_var = tk.BooleanVar(value=True)
        self.chk_autocopy = tk.Checkbutton(ctrl_row, variable=self.autocopy_var,
                                            bg=BG, command=self._on_autocopy_toggle)
        self.chk_autocopy.pack(side="left", padx=(20, 0))

        self.topmost_var = tk.BooleanVar(value=True)
        self.chk_pin = tk.Checkbutton(ctrl_row, variable=self.topmost_var,
                                       bg=BG, command=self._on_topmost)
        self.chk_pin.pack(side="left", padx=(28, 0))

    # 공개 메서드
    def set_sheets(self, names, selected=None):
        self.sheet_combo["values"] = names
        if names:
            self.sheet_var.set(selected or names[0])

    def set_result_count(self, count, total):
        if self.search_var.get().strip():
            self.result_label.config(text=f"{count}/{total}건")
        else:
            self.result_label.config(text="")

    # 내부 콜백
    def _on_file_open(self):
        if self.on_file_open: self.on_file_open()
    def _on_refresh(self):
        if self.on_refresh: self.on_refresh()
    def _on_sheet_select(self, event):
        if self.on_sheet_change: self.on_sheet_change(self.sheet_var.get())
    def _on_search_change(self, *args):
        if self.on_search: self.on_search(self.search_var.get())
    def _clear_search(self):
        self.search_var.set("")
    def _on_topmost(self):
        if self.on_topmost_toggle: self.on_topmost_toggle(self.topmost_var.get())
    def _on_opacity(self, val):
        if self.on_opacity_change: self.on_opacity_change(int(val))
    def _on_autocopy_toggle(self):
        if self.on_autocopy_toggle: self.on_autocopy_toggle(self.autocopy_var.get())
```

**포인트:**
- 콜백 패턴: `self.on_xxx = None`으로 선언, `app.py`에서 실제 함수를 연결 → 컴포넌트 간 결합도 최소화
- 설정 영역 3행의 라벨/컨트롤 분리: 라벨 줄과 컨트롤 줄을 별도 Frame으로 배치하여 시각적으로 정돈

---

### 6-6. `widget/table.py` — 테이블 뷰 (핵심)

이 파일이 프로젝트에서 가장 복잡하다. Canvas 위에 직접 셀을 그리며, 성능을 위해 **보이는 영역만 렌더링**한다.

```python
"""엑셀 데이터를 표시하는 셀 기반 테이블 위젯."""
import tkinter as tk
import tkinter.font
from core.clipboard import copy_text, copy_cells
from widget.toast import Toast

class CellTable(tk.Frame):
    # 상수
    CELL_HEIGHT = 28
    MIN_COL_WIDTH = 80
    MAX_COL_WIDTH = 250
    HEADER_BG = "#4472C4"
    HEADER_FG = "white"
    CELL_BG = "white"
    CELL_ALT_BG = "#F2F2F2"
    SELECT_BG = "#BDD7EE"
    BORDER_COLOR = "#D6D6D6"
    MATCH_BG = "#FFFF99"
    FONT = ("맑은 고딕", 10)
    HEADER_FONT = ("맑은 고딕", 10, "bold")

    def __init__(self, parent, toast: Toast, **kwargs):
        super().__init__(parent, **kwargs)
        self.toast = toast
        self.headers = []
        self.data = []
        self.filtered_indices = []
        self.col_widths = []
        self.search_term = ""
        self.auto_copy = True          # True=클릭 즉시 복사

        self._sel_start = None         # (row, col) 선택 시작점
        self._sel_end = None           # (row, col) 선택 끝점
        self._dragging = False
        self._font = None              # 폰트 캐싱
        self._header_font = None
        self._draw_pending = False     # 드래그 쓰로틀링
        self._truncate_cache = {}      # 텍스트 잘라내기 캐싱

        # Canvas + 스크롤바
        self.canvas = tk.Canvas(self, bg="white", highlightthickness=0)
        self.v_scroll = tk.Scrollbar(self, orient="vertical",
                                      command=self._on_scroll_y)
        self.h_scroll = tk.Scrollbar(self, orient="horizontal",
                                      command=self._on_scroll_x)
        self.canvas.configure(yscrollcommand=self.v_scroll.set,
                              xscrollcommand=self.h_scroll.set)

        self.canvas.grid(row=0, column=0, sticky="nsew")
        self.v_scroll.grid(row=0, column=1, sticky="ns")
        self.h_scroll.grid(row=1, column=0, sticky="ew")
        self.grid_rowconfigure(0, weight=1)
        self.grid_columnconfigure(0, weight=1)

        # 이벤트
        self.canvas.bind("<Button-1>", self._on_click)
        self.canvas.bind("<B1-Motion>", self._on_drag)
        self.canvas.bind("<ButtonRelease-1>", self._on_release)
        self.canvas.bind("<MouseWheel>", self._on_mousewheel)
        self.canvas.bind("<Configure>", self._on_configure)
        self.canvas.bind("<Control-c>", self._on_ctrl_c)
        self.canvas.configure(takefocus=True)
```

#### 핵심 메서드 설명

**데이터 로드 & 필터:**

```python
    def load(self, headers, data):
        self.headers = headers
        self.data = data
        self.filtered_indices = list(range(len(data)))
        self._truncate_cache = {}
        self._calc_col_widths()
        self.search_term = ""
        self.clear_selection()
        self._update_scroll_region()
        self._draw()

    def filter(self, term):
        self.search_term = term.strip().lower()
        if not self.search_term:
            self.filtered_indices = list(range(len(self.data)))
        else:
            self.filtered_indices = [
                i for i, row in enumerate(self.data)
                if any(self.search_term in cell.lower() for cell in row)
            ]
        self.clear_selection()
        self._update_scroll_region()
        self._draw()
```

**성능 최적화 — 가상 렌더링:**

전체 데이터를 Canvas에 그리면 1,000행만 돼도 느려진다. 핵심은 **화면에 보이는 행/열만** 그리는 것.

```python
    def _visible_row_range(self):
        canvas_h = self.canvas.winfo_height() or 400
        y_top = self.canvas.canvasy(0)
        y_bottom = self.canvas.canvasy(canvas_h)
        row_start = max(0, int((y_top - self.CELL_HEIGHT) / self.CELL_HEIGHT))
        row_end = min(len(self.filtered_indices),
                      int(y_bottom / self.CELL_HEIGHT) + 1)
        return row_start, row_end

    def _visible_col_range(self):
        canvas_w = self.canvas.winfo_width() or 650
        x_left = self.canvas.canvasx(0)
        x_right = self.canvas.canvasx(canvas_w)
        # ... 보이는 열 범위 계산 ...
        return col_start, col_end

    def _draw(self):
        self.canvas.delete("all")
        row_start, row_end = self._visible_row_range()
        col_start, col_end = self._visible_col_range()
        # 헤더와 데이터를 보이는 범위만 그림
```

**성능 최적화 — 텍스트 잘라내기 캐싱:**

셀 텍스트가 열 너비를 초과하면 "…"으로 자르는데, 매번 폰트 측정을 하면 느리다. 이진 탐색 + 캐싱으로 해결.

```python
    def _truncate(self, text, max_px):
        key = (text, max_px)
        cached = self._truncate_cache.get(key)
        if cached is not None:
            return cached
        # 이진 탐색으로 잘라내기 위치 찾기
        lo, hi = 0, len(text)
        while lo < hi:
            mid = (lo + hi + 1) // 2
            if font.measure(text[:mid] + "…") <= max_px:
                lo = mid
            else:
                hi = mid - 1
        result = text[:lo] + "…" if lo > 0 else "…"
        self._truncate_cache[key] = result
        return result
```

**성능 최적화 — 드래그 쓰로틀링:**

마우스를 빠르게 드래그하면 Motion 이벤트가 수백 번 발생한다. 16ms(~60fps) 간격으로 리드로우를 제한.

```python
    def _on_drag(self, event):
        if not self._dragging:
            return
        cell = self._pos_to_cell(event)
        if cell is not None and cell != self._sel_end:
            self._sel_end = cell
            if not self._draw_pending:
                self._draw_pending = True
                self.after(16, self._draw_throttled)

    def _draw_throttled(self):
        self._draw_pending = False
        self._draw()
```

**셀 클릭/드래그 → 복사:**

```python
    def _on_release(self, event):
        self._dragging = False
        cell = self._pos_to_cell(event)
        if cell is not None:
            self._sel_end = cell
        self._draw()
        if self.auto_copy:        # 자동복사 모드일 때만
            self._copy_selection()

    def _on_ctrl_c(self, event):  # 수동복사 모드에서 사용
        self._copy_selection()

    def _copy_selection(self):
        r1, c1, r2, c2 = self._selection_bounds()
        if r1 == r2 and c1 == c2:
            # 단일 셀 → 텍스트 복사
            copy_text(val)
        else:
            # 영역 → 탭/줄바꿈 구분 복사
            copy_cells(values)
```

---

### 6-7. `widget/app.py` — 메인 윈도우

모든 컴포넌트를 조합하고, 콜백을 연결하는 컨트롤러 역할.

```python
"""메인 애플리케이션 윈도우."""
import tkinter as tk
from tkinter import filedialog, messagebox
from core.excel_reader import ExcelReader
from widget.toolbar import Toolbar
from widget.table import CellTable
from widget.toast import Toast

class ExcelWidgetApp:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("엑셀 위젯")
        self.root.geometry("650x450")
        self.root.minsize(300, 200)
        self.root.attributes("-topmost", True)    # Always on Top
        self.root.configure(bg="#F0F0F0")
        self.reader = None

        # UI 조합
        self.toast = Toast(self.root)
        self.toolbar = Toolbar(self.root)
        self.toolbar.pack(fill="x")
        tk.Frame(self.root, height=1, bg="#CCCCCC").pack(fill="x")
        self.table = CellTable(self.root, toast=self.toast)
        self.table.pack(fill="both", expand=True)

        # 콜백 연결
        self.toolbar.on_file_open = self.open_file
        self.toolbar.on_refresh = self.refresh
        self.toolbar.on_sheet_change = self.load_sheet
        self.toolbar.on_search = self.search
        self.toolbar.on_topmost_toggle = self.set_topmost
        self.toolbar.on_opacity_change = self.set_opacity
        self.toolbar.on_autocopy_toggle = self.set_autocopy

    def run(self):
        self.root.mainloop()

    def set_autocopy(self, enabled):
        self.table.auto_copy = enabled
        mode = "자동 복사" if enabled else "Ctrl+C 복사"
        self.toast.show(f"모드: {mode}")
    # ... open_file, load_sheet, refresh, search, set_topmost, set_opacity ...
```

**포인트:**
- `attributes("-topmost", True)`: tkinter에서 Always on Top을 구현하는 핵심 한 줄
- `attributes("-alpha", 0.7)`: 투명도 조절 (0.0 ~ 1.0)
- 콜백 패턴으로 toolbar ↔ table ↔ toast 간 의존성 없이 app.py가 중개

---

## 7. 설치 & 실행 방법

### 개발 환경에서 실행

```bash
# 1. Python 3.12+ 설치 확인
python --version

# 2. 프로젝트 폴더로 이동
cd excel-widget

# 3. 의존성 설치
pip install openpyxl pyperclip

# 4. 실행
python main.py                    # 실행 후 "파일 열기"로 선택
python main.py 내파일.xlsx         # 파일 바로 열기
```

### .exe로 빌드 (배포용)

```bash
# 1. PyInstaller 설치
pip install pyinstaller

# 2. 빌드
pyinstaller --onefile --windowed --name "ExcelWidget" main.py

# 3. 결과물
# dist/ExcelWidget.exe → 이 파일 하나만 배포하면 됨
```

또는 `build.bat` 더블클릭.

---

## 8. 핵심 설계 결정과 이유

| 결정 | 이유 |
|------|------|
| tkinter (PyQt 대신) | Python 내장이라 설치 불필요, 라이선스 걱정 없음, 가벼운 유틸리티에 적합 |
| Canvas 직접 그리기 (Treeview 대신) | 개별 셀 클릭과 드래그 영역 선택을 구현하려면 Canvas가 유일한 방법 |
| 가상 렌더링 | 1만행 이상의 엑셀도 버벅임 없이 스크롤 가능 |
| 탭/줄바꿈 복사 | 엑셀/한글에 붙여넣기 시 표 구조가 유지되는 표준 방식 |
| 콜백 패턴 | 컴포넌트 간 직접 참조 없이 app.py가 중개 → 유지보수 용이 |

---

## 9. 확장 아이디어 (구현 안 됨)

| 아이디어 | 난이도 | 설명 |
|----------|--------|------|
| .xls 지원 | 낮음 | `xlrd` 라이브러리 추가 |
| 행 전체 복사 | 낮음 | 행 번호 영역 클릭 시 행 전체 선택 |
| 여러 파일 동시 열기 | 중간 | 탭 UI 추가 |
| 위치/크기 기억 | 낮음 | JSON에 저장, 다음 실행 시 복원 |
| 시스템 트레이 | 중간 | pystray로 최소화 시 트레이로 이동 |
| 단축키 열기 | 중간 | 글로벌 핫키로 위젯 토글 |
