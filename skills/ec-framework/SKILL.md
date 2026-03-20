---
name: ec-framework
description: Provides EC framework architecture knowledge, design patterns, and structure examples. Use when designing or scaffolding an EC framework architecture where components have behavior.
metadata:
  author: Bug.J
  version: "3.0"
---

# Entity-Component Framework (EC)

EC framework separates concerns via composition over inheritance. Components own both **data and behavior** for one entity aspect — this is what defines EC framework.

---

## Core Objects

### Entity

Container managing components. No domain logic.

```
Entity
  addComponent(component)
  getComponent(ComponentType) -> component
  removeComponent(ComponentType)
  destroy()
```

### Component

Encapsulates data and behavior for one entity aspect. Methods operate on own data only. Same-entity cross-component access via `entity.getComponent()`. **Never reaches into other entities.**

```
Component HealthComponent {
  // Data
  hp: int
  maxHp: int

  // Behavior
  takeDamage(amount):
    hp -= amount
    if hp < 0: hp = 0

  isAlive() -> bool:
    return hp > 0
}
```

### System (Optional)

EC framework does not require systems. Systems handle cross-entity coordination only.

```
System CollisionSystem
  update(entities):
    for entityA, entityB in pairs(entities):
      colA = entityA.getComponent(ColliderComponent)
      colB = entityB.getComponent(ColliderComponent)
      if colA.overlaps(colB):
        entityA.getComponent(HealthComponent).takeDamage(1)
```

---

## Interaction Pattern

Default to component-driven. Add a system when a component method would need to reach into another entity.

**Component-driven** — behavior stays within one component:

```
entity.getComponent(HealthComponent).takeDamage(5)
```

**System-driven** — cross-entity coordination:

```
collisionSystem.update(allEntities)
  -> detects collision
  -> calls entityA.getComponent(HealthComponent).takeDamage(1)
  -> calls entityB.getComponent(HealthComponent).takeDamage(1)
```

---

## Architecture Patterns

### Entity-Component Mapping

```
Entities:
  Player  →  PositionComponent, VelocityComponent, HealthComponent
  Enemy   →  PositionComponent, VelocityComponent, AIComponent
```

### Entity Lifecycle

```
// Create
player = createEntity()
player.addComponent(new PositionComponent(x=0, y=0))
player.addComponent(new HealthComponent(hp=3))

// Destroy
player.destroy()
```

### Game Loop

Game loop drives component updates and system updates separately:

```
loop:
  dt = getDeltaTime()

  // Component-driven updates
  for entity in entities:
    for component in entity.components:
      component.update(dt)

  // System-driven updates (if systems exist)
  collisionSystem.update(entities)
```

Component `update(dt)` handles per-entity logic (movement, animation, AI). Systems run after all components have updated.


---

## Gotchas

- **Entities MUST NOT contain domain logic** — entity manages components only
- **Components MUST NOT reach into other entities** — cross-entity logic belongs in a system
- **Deep inheritance MUST NOT be used** — favor composition over inheritance
- **God entities MUST be decomposed** — each entity should have focused, single-responsibility components
