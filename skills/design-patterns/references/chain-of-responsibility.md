# Chain of Responsibility

**Intent:** Pass a request along a chain of handlers where each handler decides to process it or forward it, decoupling sender from receiver.

## Structure

```
abstract class Handler
    next: Handler

    setNext(handler: Handler)
        next = handler

    abstract handle(request)

class ConcreteHandler extends Handler
    handle(request)
        if canHandle(request)
            process(request)
        else if next != null
            next.handle(request)

// Assembly
handlerA.setNext(handlerB)
handlerB.setNext(handlerC)
handlerA.handle(request)
```

## Example

```
// In-game hit processing: armor reduces damage, shield may absorb, health applies remainder

class ArmorHandler extends Handler
    handle(hit)
        hit.damage -= armorValue
        next.handle(hit)          // always forward reduced damage

class ShieldHandler extends Handler
    handle(hit)
        if shield.isActive
            return                // absorb — stop chain
        next.handle(hit)

class HealthHandler extends Handler
    handle(hit)
        player.health -= hit.damage   // terminal handler, no next

// Assembly
armor = new ArmorHandler(value: 10)
shield = new ShieldHandler()
health = new HealthHandler()
armor.setNext(shield)
shield.setNext(health)

armor.handle(Hit(damage: 30))
// 30 → ArmorHandler → 20 → ShieldHandler (inactive) → HealthHandler applies 20
```

## Trade-offs

+ Handlers can be reordered, added, or removed at runtime without touching the client
+ Each handler has a single focused responsibility
- No guarantee the request is handled — can be silently dropped
- Long chains obscure control flow and add traversal overhead

## Related

- **Decorator** — also chains objects, but every decorator always forwards; CoR may stop early
- **Command** — requests passed along the chain are often encapsulated as Command objects
- **Composite** — a handler's next can itself be a Composite of handlers
