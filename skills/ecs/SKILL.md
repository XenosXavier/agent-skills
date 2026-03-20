---
name: ecs
description: Provides pure ECS architecture knowledge, design patterns, and structure examples. Use when designing or scaffolding a pure ECS architecture.
metadata:
  author: Bug.J
  version: "3.0"
---

# Entity Component System (ECS)

Pure ECS strictly separates data from behavior. Everything is composed from three building blocks — **Entity**, **Component**, **System** — coordinated by a **World** through a storage-based query mechanism.

---

## Core Objects

### Entity

A unique integer ID. Entities don't hold data or behavior — they are just identifiers used to associate components together.

```
EntityID = 0   // Player
EntityID = 1   // Enemy
```

### Component

Pure data for one entity aspect. Fields only — **no methods, no logic**. Logic in a component is a sign that it has drifted into component-based design, which is not pure ECS.

```
Component Position {
  x: float
  y: float
}

Component Velocity {
  vx: float
  vy: float
}
```

### System

The only place logic lives. A system declares which components it needs, queries World for matching entities, and processes them each tick. Systems are stateless — no instance fields.

```
System MovementSystem
  requires: Position, Velocity

  update(world, dt):
    for entity in world.query(Position, Velocity):
      pos = world.get(entity, Position)
      vel = world.get(entity, Velocity)
      pos.x += vel.vx * dt
      pos.y += vel.vy * dt
```

### World

Central manager. Owns all entity IDs and per-type component Storages. Systems interact with the world through its API — they never touch Storage directly.

```
world.createEntity()                        -> EntityID
world.addComponent(entity, component)
world.getComponent(entity, ComponentType)   -> component
world.removeComponent(entity, ComponentType)
world.destroyEntity(entity)
world.query(ComponentType, ...)             -> [EntityID, ...]
```

### Storage

World maintains one Storage per component type — a table keyed by entity ID:

```
Storage<Position>:  { 0 → {x:120, y:300},  1 → {x:800, y:300} }
Storage<Velocity>:  { 0 → {vx:0,  vy:0},   1 → {vx:-200, vy:0} }
```

`world.query(Position, Velocity)` returns the intersection — only entities present in **all** requested Storages. Adding or removing a component immediately changes which systems process an entity.

---

## Architecture Patterns

### Entity-Component Mapping

```
Entities:
  Player    →  Position, Velocity
  Enemy     →  Position, Velocity, AI
```

### System Splitting

Keep a system focused on one responsibility. Split when a system handles multiple distinct phases or when its output needs to be reused by other systems.

Example — collision detection:

**Single system** — all logic (broad phase, narrow phase, response) in one `CollisionSystem`. Use when < 50 objects or no event reuse needed.

**Split systems** — use when > 100 objects or collision events need reuse:
- `BroadPhaseCollisionSystem` — fast AABB pre-filter; outputs candidate pairs via shared component
- `NarrowPhaseCollisionSystem` — precise check; delegates shape math to utility outside ECS
- `CollisionResponseSystem` — applies responses (push apart, damage, events)

Shape math goes in a utility, not in a system.

### Entity Lifecycle

By default, systems create and destroy entities directly via `world.createEntity()` / `world.destroyEntity()`. Add dedicated lifecycle systems when creation or destruction logic needs to be system-driven or deferred:
- `SpawnSystem` — reads spawn requests from a component, calls `world.createEntity()` + `addComponent`
- `DespawnSystem` — reads destroy requests from a component, calls `world.destroyEntity()`

---

## System Interaction Rules

**System MUST NOT:**
- Hold entity references across frames
- Modify entity structure directly
- Call other systems
- Store instance state

**System MUST:**
- Query World for entities
- Read and write component data
- Communicate with other systems via shared components

---

## Gotchas

- **Shape math and algorithms MUST NOT be placed in systems** — they belong in utilities outside ECS
- **State in a system MUST be moved to a singleton component** — systems must be stateless
- **God entities MUST be decomposed** — each entity should have focused, single-responsibility components
- **A system with too many component dependencies MUST be split** — into focused, single-responsibility systems
- **Systems MUST NOT call other systems directly** — communicate through shared components
