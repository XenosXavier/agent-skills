---
name: technical-design-document
description: Provides Game Technical Design Document (TDD) knowledge, section structure, and content templates. Use when generating or updating content for a specific TDD section.
metadata:
  author: Bug.J
  version: "3.0"
---

# Game Technical Design Document (TDD)

A TDD is the implementer's counterpart to a GDD. It defines how the game is built — architecture, code structure, technical constraints, and delivery process — without describing gameplay or design intent (which belong in GDD).

## Sections

Only sections listed below are permitted. Do not add, rename, or reorder without user approval.

### 📝 Changelog

Version history. Reverse-chronological order.

| Version | Date | Authors | Changes |
|---------|------|---------|---------|
| v0.1 | 2025-03-19 | Bug.J & Claude | Initial draft — all sections created |

Version metadata belongs here only. Use @mention for authors if supported, otherwise plain text.

---

### 📋 GDD Summary

- **Include when**: Only if no GDD exists
- **Allowed sources**: User input only

Key GDD features that affect technical implementation.

---

### 🎯 Technical Goals

Numbered list of measurable goals.

1. 60 FPS on target platform
2. Load time under 3 seconds

---

### ⚠️ Technical Risks

Known challenges, their severity, and mitigation strategies.

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Algorithm too slow for large boards | Medium | High | Prototype early; profile in Sprint 1 |

---

### 🏗️ Code Organization

Empty by default. Populate only if user has existing UML or module map. Do NOT infer from GDD.

If available:
- Module descriptions and responsibilities
- Dependencies between modules

---

### 📐 Code Style Guidelines

Naming conventions, file structure, and comment policy.

- **Language & Standard**: e.g. `TypeScript strict`, `C++17`
- **Naming**: variables, functions, classes, constants
- **File structure**: folder layout, naming conventions
- **Comments**: when and how

Format per rule:

```
Rule: Constants in SCREAMING_SNAKE_CASE
✅ MAX_SPEED = 800
❌ maxSpeed = 800
```

---

### 🌿 Branching Policy

Branch naming rules, commit conventions, and merge strategy.

Solo developers:
- **Main**: always releasable
- **Feature**: `feature/short-description`
- **Commits**: Conventional (`feat:`, `fix:`, `chore:`)
- **Merge**: squash or rebase

Teams: add PR rules, reviewers, freeze policy.

---

### ⚡ Performance Budgets

FPS targets, memory limits, and per-system time budgets. Express in ms.

| Metric | Target | Hard Limit |
|--------|--------|------------|
| Frame time | 16.6 ms (60 FPS) | 33.3 ms (30 FPS) |
| Gameplay | ≤ 3 ms | 5 ms |
| Memory | ≤ 512 MB | 1 GB |

---

### 🚀 Build Delivery

Build process, delivery method, and release schedule.

- **Build process**: how compiled/bundled — e.g. `tsc`, Webpack
- **Delivery**: how reaches users — e.g. GitHub Pages, itch.io
- **Schedule**: cadence or milestone-based

---

### 🗂️ Version List

Planned milestones and required deliverables per version.

| Version | Target Date | Required Features |
|---------|------------|------------------|
| v0.1 Alpha | 2025-04-15 | Core loop, placeholders |
| v1.0 Release | 2025-06-30 | Polished, tested, complete |

---

### 🖥️ Platform & Requirements

Target platform, minimum and recommended specs, and external dependencies.

| | Minimum | Recommended |
|-|---------|-------------|
| OS | Windows 10 | Windows 11 |
| Browser | Chrome 90+ | Chrome Latest |
| RAM | 4 GB | 8 GB |
| Disk | 500 MB | 1 GB |

External dependencies: runtimes, SDKs, installers.

---

### 🛠️ External Libraries & Tools

Libraries, middleware, engines, and tools used in the project.

| Library / Tool | Version | Purpose |
|----------------|---------|---------|
| Phaser | 3.80 | Game engine |
| Howler.js | 2.2 | Audio |


---

## Format

- **Document title**: Clean and concise — e.g. `Game Technical Design Document`
- **Page names**: Emoji + title — e.g. `🎯 Technical Goals`
- **Language**: English unless user specifies
- **Version, date, author**: Belongs in 📝 Changelog only. Use @mention for authors if supported, otherwise plain text.
- **Tables**: Bold header row — e.g. `| **Item** | **Details** |`
- **Unresolved items**: Mark as `**TBD**`, never leave blank
- **Versioning**: Semantic — `MAJOR.MINOR.PATCH`
  - MAJOR: milestone (alpha → beta → release)
  - MINOR: new system or feature
  - PATCH: fixes, tweaks, docs
- **Diagrams**: Prefer Mermaid for flowcharts, UML, game loops. Always caption diagrams.

## Gotchas

- **Gameplay design intent MUST NOT appear in the TDD** — mechanics, narrative, and art direction belong in GDD
- **MUST NOT write unconfirmed or assumed content** — mark unresolved items as `**TBD**`, never leave blank
- **When a GDD exists, MUST NOT include a GDD Summary section** — reference the GDD directly instead
- **Code Organization MUST NOT be inferred from the GDD** — populate only from user-provided UML or module maps
- **Consistent terminology MUST be maintained across all sections** — one name per concept
- **When a feature changes, identify all affected sections before updating** — never update in isolation
- **MUST NOT add a new section without user confirmation** — only sections listed above are permitted
