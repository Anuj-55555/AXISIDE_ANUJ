# Axis Editor and IDE
### Java Programming Lab Project — Scilab Script Editor

---

## Phase Status

| Phase | Goal | Status |
|-------|------|--------|
| 1 | Basic UI — open, edit, save | ✅ Complete |
| 2 | Syntax highlighting, smart editing, find/replace | ✅ Complete |
| 3 | Run scripts via Scilab CLI, console output | ✅ Complete |
| 4 | Error detection, line highlighting, error panel | ✅ Complete |
| 5 | File explorer, tabbed editing, recent files, toolbar icons | ✅ Complete |
| 6 | Testing and documentation | 🔲 Final |

---

## Prerequisites

| Tool | Version | Download |
|------|---------|---------|
| Java JDK | 17 or later | https://adoptium.net |
| Apache Maven | 3.8+ | https://maven.apache.org |
| Scilab | 6.x or 2024.x | https://www.scilab.org/download |

Verify your setup:
```cmd
java -version
mvn -version
```

---

## Build and Run

```cmd
cd C:\Users\HP\Desktop\AxisIDE
C:\apache-maven-3.9.14\bin\mvn.cmd clean package
java -jar target\axis-ide.jar
```

On second and later runs (Maven already downloaded dependencies):
```cmd
cd C:\Users\HP\Desktop\AxisIDE
C:\apache-maven-3.9.14\bin\mvn.cmd clean package
java -jar target\axis-ide.jar
```

---

## Scilab Setup (required for Run button)

The IDE needs to find the Scilab CLI executable. Set this before running:

**Windows — in the same cmd window:**
```cmd
set SCILAB_HOME=C:\Program Files\scilab-6.1.1
java -jar target\axis-ide.jar
```

**Windows — permanent (recommended):**
1. Search → "Edit the system environment variables"
2. Environment Variables → System variables → New
3. Variable name: `SCILAB_HOME`
4. Variable value: `C:\Program Files\scilab-6.1.1`
5. OK → OK → OK
6. Open a new cmd window — works from now on automatically

> **Note:** Scilab is case-sensitive. `disp` works. `Disp` and `DISP` do not.

---

## Project Structure

```
AxisIDE/
├── pom.xml                               Maven build file
└── src/main/java/com/axiseditor/
    ├── Main.java                          Entry point
    │
    ├── editor/
    │   ├── EditorPanel.java               Single-tab editor (used inside each tab)
    │   ├── ErrorHighlighter.java          Red line + wavy underline + gutter icon
    │   ├── FindReplaceDialog.java         Ctrl+F / Ctrl+H dialog
    │   └── scilab/
    │       ├── ScilabTokenMaker.java      Custom Scilab syntax tokeniser
    │       ├── ScilabTokenMakerFactory.java  Registers "text/scilab" MIME type
    │       ├── ScilabTheme.java           VS Code dark colour scheme
    │       ├── BracketColorizer.java      Rainbow bracket/quote depth colours
    │       ├── ScilabTemplateEngine.java  Code templates on Enter (for, if, ...)
    │       ├── SmartTypingHandler.java    Wrap selection, auto-close, Ctrl+/
    │       └── ScilabAutoComplete.java    Keyword + variable completion popup
    │
    ├── execution/
    │   ├── ScilabRunner.java              ProcessBuilder Scilab launcher
    │   ├── ExecutionManager.java          Run/Stop coordinator, tab-aware
    │   └── ErrorParser.java              Parse Scilab stderr for line numbers
    │
    ├── filemanager/
    │   ├── FileManager.java               Tab-aware file I/O + recent files
    │   └── RecentFilesManager.java        Persists last 10 opened files
    │
    ├── ui/
    │   ├── MainWindow.java                Root JFrame, 3-column layout
    │   ├── TabbedEditorPanel.java         Multi-tab editor container
    │   ├── ToolbarPanel.java              Buttons with icons + Recent Files menu
    │   ├── ConsolePanel.java              Coloured output area
    │   ├── ErrorPanel.java                Clickable error list
    │   ├── StatusBar.java                 Filename · Ln/Col · File type
    │   └── filetree/
    │       └── FileExplorerPanel.java     Left sidebar file tree
    │
    └── utils/
        └── UIConstants.java               Fonts, colours, sizes
```

---

## Application Layout

```
┌──────────────────────────────────────────────────────────────────┐
│  New · Open · Recent▾ · Save · Save As  |  +Tab · ✕Tab  |  ▶ ■  │  ← Toolbar
├───────────────┬────────────────────────────┬────────────────────┤
│               │  Tab 1  │  Tab 2  │  +     │                    │
│  File         ├────────────────────────────┤  Console Output    │
│  Explorer     │                            │  (stdout/stderr)   │
│  (sidebar)    │   Active Editor Tab        ├────────────────────┤
│               │   (RSyntaxTextArea)        │  Error Panel       │
│               │                            │  (click to jump)   │
├───────────────┴────────────────────────────┴────────────────────┤
│  filename.sce ●            Ln 12, Col 5           Scilab .sce   │  ← Status Bar
└──────────────────────────────────────────────────────────────────┘
```

---

## All Features by Phase

### Phase 1 — Basic UI
- Open, edit, and save `.sce` / `.sci` files
- Unsaved changes indicator (●) in status bar
- Prompt to save before closing or creating a new file
- Console panel for file operation feedback

### Phase 2 — Smart Editor

**Syntax Highlighting (VS Code dark theme)**

| Token | Colour |
|-------|--------|
| Keywords (`for`, `if`, `while`, ...) | Blue |
| Built-in functions (`disp`, `sqrt`, ...) | Yellow |
| String literals (`'text'`, `"text"`) | Orange |
| Number literals | Green |
| Comments (`//`, `/* */`) | Muted green |
| Variables / identifiers | Light blue |

**Rainbow Bracket Colours**

| Nesting depth | Colour |
|---------------|--------|
| 0 | Gold |
| 1 | Teal |
| 2 | Violet |
| 3 | Salmon |

Applies to `( )`, `[ ]`, `{ }`, `' '`, `" "`

**Code Templates — press Enter after keyword**

| Type | Get |
|------|-----|
| `for` | `for i = 1:n` / body / `end` |
| `while` | `while condition` / body / `end` |
| `if` | `if condition then` / body / `end` |
| `function` | `function [out] = name(args)` / body / `endfunction` |
| `select` | `select expr` / `case` / `otherwise` / `end` |
| `try` | `try` / body / `catch` / body / `end` |

Caret lands at the first editable slot. Nested templates indent correctly.

**Smart Typing**

| Action | Result |
|--------|--------|
| Select `hello`, type `(` | `(hello)` |
| Select `hello`, type `"` | `"hello"` |
| Select `hello`, type `[` | `[hello]` |
| Type `(` alone | `(│)` caret inside |
| Type `[` alone | `[│]` caret inside |
| Type `"` alone | `"│"` caret inside |
| Cursor at `(│)`, type `)` | Skip over — no duplicate |
| Cursor at `(│)`, Backspace | Delete both characters |

**Other Phase 2 features**
- Ctrl+/ — toggle `//` comment on current or selected lines
- Ctrl+F — Find dialog (next, previous, wrap-around)
- Ctrl+H — Find & Replace (regex, match case, whole word)
- Ctrl+Z / Ctrl+Y — Undo / Redo (unlimited history)
- Ctrl+Space — Force autocomplete popup
- Automatic autocomplete after 300ms (keywords, builtins, user variables)

### Phase 3 — Script Execution
- Ctrl+R or ▶ Run button — saves then executes via `scilab-cli`
- ■ Stop button — kills running process immediately
- Live stdout streaming to console (white text)
- Live stderr streaming to console (red text)
- Exit code displayed with ✔ / ✘ indicator
- Auto-saves unsaved files before running

### Phase 4 — Error Intelligence
- After execution, stderr is parsed for all error line numbers
- Red background painted on every error line
- Wavy red underline drawn under error line text
- Red `●!` gutter icon with tooltip on every error line
- Error Panel (below console) lists all errors as clickable rows
- Clicking a row scrolls editor to that line and selects it
- All error highlights clear automatically when you start typing

### Phase 5 — Full IDE Experience

**Multi-tab Editing**
- Each tab has its own editor, undo history, file, and error state
- Tab title turns orange and shows ● when modified
- × button on each tab closes it (prompts to save if dirty)
- Ctrl+T — new tab
- Ctrl+W — close current tab
- Ctrl+Tab / Ctrl+Shift+Tab — cycle through tabs
- Middle-click tab — close it
- Double-click empty tab bar space — new tab

**File Explorer Sidebar**
- Lazy-loaded file tree (folders expand on click)
- Folders shown in gold, Scilab files in green
- Double-click file — opens in new tab
- Right-click menu: Open, Set as Root, Refresh
- Browse `…` button — choose any root folder
- Refresh `↺` button

**Recent Files**
- Open Recent ▾ dropdown in toolbar — last 10 files
- Auto-updated on every open and save
- Clear button to reset the list
- Stored in `~/.axiseditor/recent.properties`

**Status Bar**
- Left: current filename (hover for full path)
- Centre: live Ln N, Col N — updates as cursor moves
- Right: file type badge (Scilab .sce / Scilab .sci)

---

## Keyboard Shortcuts — Complete Reference

| Shortcut | Action |
|----------|--------|
| Ctrl+N | New file |
| Ctrl+O | Open file |
| Ctrl+S | Save |
| Ctrl+Shift+S | Save As |
| Ctrl+T | New tab |
| Ctrl+W | Close current tab |
| Ctrl+Tab | Next tab |
| Ctrl+Shift+Tab | Previous tab |
| Ctrl+R | Run script |
| Ctrl+F | Find |
| Ctrl+H | Find and Replace |
| Ctrl+Z | Undo |
| Ctrl+Y | Redo |
| Ctrl+/ | Toggle comment |
| Ctrl+Space | Force autocomplete |
| Enter | Smart template after keyword |

---

## Team Task Distribution

| Member | Role | Owns |
|--------|------|------|
| Developer 1 — Editor Engineer | All Phase 2 editor features | `editor/scilab/` package |
| Developer 2 — Execution Engineer | Phase 3 run/stop, Phase 4 errors | `execution/` package, `ConsolePanel`, `ErrorPanel`, `ErrorHighlighter` |
| Developer 3 — UI & System Engineer | Phase 1/5 window, tabs, file tree | `ui/` package, `filemanager/` package |

---

## Common Issues and Fixes

**`Undefined variable: Disp`**
Scilab is case-sensitive. The correct function is `disp` (all lowercase).
All Scilab built-ins are lowercase. Use autocomplete (Ctrl+Space) to avoid typos.

**Run shows "Scilab not found"**
Set `SCILAB_HOME` before running the jar:
```cmd
set SCILAB_HOME=C:\Program Files\scilab-6.1.1
java -jar target\axis-ide.jar
```

**BUILD FAILURE during `mvn clean package`**
Check Java version — needs 17 or higher:
```cmd
java -version
```

**Maven command not found**
Use the full path:
```cmd
C:\apache-maven-3.9.14\bin\mvn.cmd clean package
```

**Autocomplete not appearing**
Type at least one character and wait 300ms, or press Ctrl+Space.

**Templates not triggering on Enter**
The keyword must be the only thing on the line. Type `for` alone and press Enter.

**Recent files menu is empty**
The list builds up as you open and save files. It persists between sessions in `~/.axiseditor/recent.properties`.
