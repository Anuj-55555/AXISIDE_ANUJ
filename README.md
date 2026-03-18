# Axis Editor and IDE
### Java Programming Lab Project

---

## Phase 1 — Project Setup & Basic UI ✅

### What's built
| Component | File | Description |
|-----------|------|-------------|
| `Main` | `Main.java` | Entry point, starts Swing on EDT |
| `MainWindow` | `MainWindow.java` | Root JFrame, wires all panels together |
| `ToolbarPanel` | `ToolbarPanel.java` | New / Open / Save / Save As buttons |
| `EditorPanel` | `EditorPanel.java` | RSyntaxTextArea + line numbers |
| `ConsolePanel` | `ConsolePanel.java` | Read-only output area |
| `StatusBar` | `StatusBar.java` | File path + unsaved indicator |
| `FileManager` | `FileManager.java` | All file I/O, unsaved-changes dialog |
| `UIConstants` | `UIConstants.java` | Fonts, colors, sizes |

---

## Prerequisites

| Tool | Version | Download |
|------|---------|---------|
| Java JDK | 17 or later | https://adoptium.net |
| Apache Maven | 3.8+ | https://maven.apache.org |

Check you have both:
```bash
java -version
mvn -version
```

---

## How to Build & Run

### Step 1 — Navigate to the project folder
```bash
cd AxisIDE
```

### Step 2 — Download dependencies and compile
```bash
mvn clean package
```
Maven will download `rsyntaxtextarea` and `autocomplete` from Maven Central.
This only needs internet access the first time.

### Step 3 — Run the IDE
```bash
java -jar target/axis-ide.jar
```

Or use Maven's exec plugin:
```bash
mvn exec:java -Dexec.mainClass="com.axiseditor.Main"
```

---

## Project Structure

```
AxisIDE/
├── pom.xml                              ← Maven build file (dependencies here)
└── src/
    └── main/
        └── java/
            └── com/axiseditor/
                ├── Main.java            ← Entry point
                ├── ui/
                │   ├── MainWindow.java  ← JFrame + layout
                │   ├── ToolbarPanel.java
                │   ├── ConsolePanel.java
                │   └── StatusBar.java
                ├── editor/
                │   └── EditorPanel.java ← RSyntaxTextArea wrapper
                ├── filemanager/
                │   └── FileManager.java ← File I/O logic
                └── utils/
                    └── UIConstants.java ← Styling constants
```

---

## Phase Roadmap

| Phase | Goal | Status |
|-------|------|--------|
| 1 | Basic UI — open, edit, save | ✅ Done |
| 2 | Syntax highlighting, line numbers, bracket matching | 🔲 Next |
| 3 | Run scripts via Scilab CLI | 🔲 Upcoming |
| 4 | Error detection + line highlighting | 🔲 Upcoming |
| 5 | File explorer, toolbar icons, recent files | 🔲 Upcoming |
| 6 | Testing & documentation | 🔲 Final |

---

## Team Task Reference (Phase 1)

| Member | Role | Phase 1 Files |
|--------|------|---------------|
| Developer 1 — Editor Engineer | `EditorPanel.java` | Owns editor component |
| Developer 2 — Execution Engineer | `ConsolePanel.java` | Owns output display |
| Developer 3 — UI & System Engineer | `MainWindow`, `ToolbarPanel`, `FileManager`, `StatusBar` | Owns window + file I/O |

---

## Dependency Notes

The key library used is **RSyntaxTextArea** by fifesoft:
- `rsyntaxtextarea` — the editor component (line numbers, bracket matching)
- `autocomplete` — companion library (used in Phase 4)

Both are declared in `pom.xml` and downloaded automatically by Maven.

---

## Common Issues

**`java: error: release version 17 not supported`**
→ Update your JDK to Java 17+, or change `<maven.compiler.source>` in `pom.xml` to `11`.

**Window appears but editor is blank**
→ Normal — Phase 1 opens with an empty "Untitled" file. Use File → Open to load a `.sce` file.

**Maven can't download dependencies**
→ Check internet connection. Corporate networks may need a proxy configured in `~/.m2/settings.xml`.


