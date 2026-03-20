# Template Method

**Intent:** Define an algorithm's fixed skeleton in a base class and let subclasses override only the variable steps without changing the overall sequence.

## Structure

```
abstract class AbstractClass
    templateMethod()       // final — subclasses must not reorder steps
        stepOne()          // invariant — implemented here
        stepTwo()          // abstract — subclass must implement
        stepThree()        // hook — optional override with default
        stepFour()         // invariant — implemented here

    stepOne()  -> // concrete
    abstract stepTwo()
    stepThree() -> // default no-op or sensible default
    stepFour() -> // concrete

class ConcreteClassA extends AbstractClass
    stepTwo()   -> // variant A
    stepThree() -> // optional: override hook

class ConcreteClassB extends AbstractClass
    stepTwo()   -> // variant B
    // stepThree uses default
```

## Example

```
// Enemy AI turn loop: sense → decide → execute → animate is always the same;
// each enemy type overrides only the decide step (and optionally animate)

abstract class EnemyAI
    takeTurn(world: World)                  // template method
        targets = sense(world)
        action  = decide(targets)           // abstract
        execute(action, world)
        playAnimation(action)               // hook

    sense(world): List<Target>
        return world.getVisibleTargets(this, sightRange)

    abstract decide(targets: List<Target>): Action

    execute(action, world)
        action.apply(this, world)

    playAnimation(action)
        animator.play(action.defaultAnimation)   // default

class ZombieAI extends EnemyAI
    decide(targets)
        nearest = targets.sortBy(distance).first
        return new MoveAction(toward: nearest)

class ArcherAI extends EnemyAI
    decide(targets)
        weakest = targets.sortBy(health).first
        if distance(weakest) < attackRange
            return new RangedAttackAction(target: weakest)
        return new MoveAction(toward: weakest)

    playAnimation(action)
        if action is RangedAttackAction -> animator.play("bow_shoot")
        else                            -> super.playAnimation(action)

```

## Trade-offs

+ Invariant algorithm skeleton lives in one place — subclasses can only customize designated steps
+ Adding a new variant means a new subclass, not modifying the base algorithm
- Inheritance-based — base class changes can break all subclasses (fragile base class)
- Cannot vary steps at runtime; use Strategy when runtime swapping is needed

## Related

- **Strategy** — both vary part of an algorithm; Strategy uses composition at runtime; Template Method uses inheritance at compile time
- **Factory Method** — a hook step in a Template Method is often a Factory Method
