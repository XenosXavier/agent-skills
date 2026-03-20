# Null Object

**Intent:** Eliminate null checks by substituting a do-nothing implementation of an interface that callers can use unconditionally.

## Structure

```
interface Logger
    log(level, message) -> void
    isEnabled(level) -> bool

class ConsoleLogger implements Logger
    log(level, message) -> print("[" + level + "] " + message)
    isEnabled(level)    -> true

// Null implementation — safe to call, does nothing
class NullLogger implements Logger
    log(level, message) -> /* no-op */
    isEnabled(level)    -> false

// Client never checks for null
class OrderService
    constructor(logger: Logger = NullLogger())

    placeOrder(order)
        logger.log("INFO", "Placing order " + order.id)
        // ... business logic ...
        logger.log("INFO", "Order placed")

// Wiring
prodService = OrderService(ConsoleLogger())  // real logging
testService = OrderService()                 // NullLogger by default — silent
```

## Example

```
// Game — characters may have a human controller, AI controller, or no controller (spectator slot)

interface InputController
    getMovementInput() -> Vector2
    getAttackInput()   -> bool
    getAbilityInput()  -> int | null

class HumanController implements InputController
    getMovementInput() -> readGamepad() or readKeyboard()
    getAttackInput()   -> isKeyDown("Space")
    getAbilityInput()  -> readAbilityKeyPress()

class AIController implements InputController
    constructor(behaviourTree: BehaviourTree)
    getMovementInput() -> behaviourTree.computeMovement()
    getAttackInput()   -> behaviourTree.shouldAttack()
    getAbilityInput()  -> behaviourTree.chooseAbility()

// Null Object: spectator or disconnected player — character stands idle
class NullController implements InputController
    getMovementInput() -> Vector2(0, 0)
    getAttackInput()   -> false
    getAbilityInput()  -> null

// Character never branches on "do I have a controller?"
class Character
    constructor(controller: InputController)
    update(deltaTime)
        move(controller.getMovementInput())
        if controller.getAttackInput() -> performAttack()
        ability = controller.getAbilityInput()
        if ability != null -> useAbility(ability)

// Wiring
player1   = Character(HumanController())
enemyBot  = Character(AIController(patrolTree))
spectator = Character(NullController())     // no null checks anywhere
```

## Trade-offs

+ Eliminates repetitive null guard clauses throughout the codebase
+ Callers interact with one code path — simpler and more readable
+ Excellent test double: no mocking framework required
- Can silently swallow errors when absence should surface as a bug
- Every interface method must be implemented as a no-op; large interfaces become burdensome

## Related

- **Strategy** — Null Object is commonly used as the default Strategy when none is configured
- **Special Case (Fowler)** — generalisation: any domain-specific default behaviour (e.g. `GuestUser`) rather than just a no-op
- **Proxy** — structurally similar; Proxy controls access while Null Object represents absence
