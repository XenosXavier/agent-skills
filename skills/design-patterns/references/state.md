# State

**Intent:** Let an object change its behavior at runtime by delegating to a swappable state object instead of branching on an internal flag.

## Structure

```
interface State
    enter(ctx: Context)
    exit(ctx: Context)
    handleInput(ctx: Context, input: Input)
    update(ctx: Context)

class Context
    currentState: State

    transitionTo(newState: State)
        currentState.exit(this)
        currentState = newState
        currentState.enter(this)

    handleInput(input) -> currentState.handleInput(this, input)
    update()           -> currentState.update(this)

class ConcreteState implements State
    handleInput(ctx, input)
        if condition
            ctx.transitionTo(new OtherState())
```

## Example

```
// Platformer character: Idle, Running, Jumping — each handles input and physics differently

class PlayerIdleState implements State
    enter(player)  -> player.animator.play("idle")
    exit(player)   -> // nothing

    handleInput(player, input)
        if input.left or input.right -> player.transitionTo(new PlayerRunState())
        if input.jump                -> player.transitionTo(new PlayerJumpState())

    update(player) -> player.applyGravity()

class PlayerRunState implements State
    enter(player) -> player.animator.play("run")

    handleInput(player, input)
        if not (input.left or input.right) -> player.transitionTo(new PlayerIdleState())
        if input.jump                      -> player.transitionTo(new PlayerJumpState())

    update(player)
        player.moveHorizontal(input.direction * player.speed)

class PlayerJumpState implements State
    enter(player)
        player.animator.play("jump")
        player.velocity.y = player.jumpForce

    handleInput(player, input) -> // locked mid-air

    update(player)
        player.applyGravity()
        if player.isGrounded -> player.transitionTo(new PlayerIdleState())

class Player   // Context
    currentState: State = new PlayerIdleState()

    transitionTo(newState)
        currentState.exit(this)
        currentState = newState
        currentState.enter(this)

    update()
        currentState.handleInput(this, inputSystem.getInput())
        currentState.update(this)
```

## Trade-offs

+ Eliminates large if/switch blocks — each state class handles only its own behavior
+ Transitions are explicit and self-documenting inside state classes
- More classes — one per state, which can feel excessive for simple FSMs
- State objects may need access to Context internals, creating coupling

## Related

- **Strategy** — structurally identical; Strategy is chosen externally by the client; State drives transitions from within the object's own lifecycle
- **Flyweight** — stateless State objects can be shared as Flyweights across many Context instances
