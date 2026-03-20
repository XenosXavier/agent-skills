---
name: game-design-document
description: Provides Game Design Document (GDD) knowledge, section structure, and content templates. Use when generating or updating content for a specific GDD section.
metadata:
  author: Bug.J
  version: "3.0"
---

# Game Design Document (GDD)

A Game Design Document is the authoritative reference for a game's design. It defines what the game is, how it plays, what it looks like, and what it needs to be built — without implementation details (which belong in TDD).

## Sections

Only sections listed below are permitted. Do not add, rename, or reorder without user approval.

### 📝 Changelog

Version history. Reverse-chronological order.

| Version | Date | Authors | Changes |
|---------|------|---------|---------|
| v0.1 | 2025-03-19 | Bug.J & Claude | Initial draft — all sections created |

Version metadata belongs here only, not in Overview.

---

### 📋 Overview

Game summary, platform, genre, controls, and high-level design goals.

- **Status banner**: If draft, add: `⚠️ **Status: Draft** — Work in progress.` Otherwise omit.
- **Game Summary**: 2–3 sentences describing concept
- **Core Positioning**: Table with Genre, Platform, Controls, Game Mode, Target Audience, Business Model (only what applies)
- **Design Pillars**: 3–5 guiding principles defining design philosophy
- **Design Goals**: Concrete, verifiable intentions
- **Non-Goals**: Features explicitly out of bounds

---

### 🎮 Gameplay

Core loop, controls, objectives, difficulty progression, and fail conditions.

- **Core Loop**: Description or diagram of main gameplay loop
- **Controls**: Table mapping actions to inputs (only applicable platforms)
- **Difficulty Progression**: How and when game becomes harder
- **Objectives**: What player tries to achieve
- **Fail / End Conditions**: What causes lose or game over
- **Progression / Rewards** *(if applicable)*: How player advances or unlocks

---

### 🔧 Mechanics

Design-level behavior of core systems. Describe *what* and *why*, not *how*.

Common systems:

- **Collision**: How detected, what triggers outcome
- **Object Generation**: How and when objects spawn
- **Speed / Difficulty Progression**: How pace changes
- **Physics** *(if applicable)*: Movement, gravity, momentum
- **AI / Pathfinding** *(if applicable)*: How NPCs move or act
- **Save / Persistence** *(if applicable)*: What saved and when

---

### 🎯 Game Elements

Player characters, enemies, obstacles, items, and environment.

Consistent structure:

```
## Category
### Object Name
| Attribute | Details |
```

Common categories:

- **Player Character(s)**: Stats, abilities, movement, animations
- **Enemies / Obstacles**: Types, behaviors, spawn, attacks
- **Items / Collectibles** *(if applicable)*: Effects, pickup, spawn
- **Environment**: Background, platforms, hazards

Only document confirmed objects.

---

### 🎨 Art & Audio

Visual style, animation specs, and audio design.

- **Visual Style**: Aesthetic, color palette, mood
- **Animation Specs**: Table of animated elements and frame counts (confirmed only)
- **Audio Design**: Audio approach. If no audio, state explicitly. No speculative plans.

---

### ⚙️ Technical Specs

Engine, platform, language, display, input, storage, and dependencies.

- **Platform & Runtime**: Table with platform, rendering, language, entry point
- **Display**: Aspect ratio, resolution, color depth, frame rate
- **Input**: Input methods and mappings
- **Storage**: How data persisted
- **Dependencies**: Libraries, frameworks, constraints

---

### 🖥️ UI / UX Design

Screen flow, HUD layout, key screens, and accessibility considerations.

- **Screen Flow**: Description or diagram of screen connections
- **HUD**: Table of elements, positions, information
- **Key Screens**: Each major screen includes:
  - What shown
  - What player can do
  - What happens
- **Accessibility** *(if applicable)*: Design decisions for accessibility

---

## Format

- **Document title**: Clean and concise — e.g. `Game Design Document`
- **Page names**: Emoji + title — e.g. `🎮 Gameplay`
- **Language**: English unless user specifies
- **Version, date, author**: Belongs in 📝 Changelog only. Use @mention for authors if supported, otherwise plain text.
- **Tables**: Bold header row — e.g. `| **Item** | **Details** |`
- **Diagrams**: If a section needs a diagram, refer to [DIAGRAM-GUIDE.md](references/DIAGRAM-GUIDE.md) to choose between Mermaid and matplotlib.

---

## Gotchas

- **Implementation details MUST NOT appear in the Game Design Document** — algorithms, formulas, and code-level decisions belong in TDD
- **MUST NOT write unconfirmed or assumed content** — every detail must be explicitly provided or approved by the user
- **Consistent terminology MUST be maintained across all sections** — one name per concept (e.g. `Dino` not `T-Rex`)
- **When a feature changes, identify all affected sections before updating** — never update in isolation
- **MUST NOT add a new section without user confirmation** — only sections listed above are permitted
