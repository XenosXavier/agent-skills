# Observer

**Intent:** Let a subject automatically notify an open-ended set of dependents when its state changes, without knowing who they are.

## Structure

```
interface Observer
    update(event: Event)

class Subject
    observers: List<Observer>

    subscribe(obs: Observer)   -> observers.add(obs)
    unsubscribe(obs: Observer) -> observers.remove(obs)

    notify(event: Event)
        for each obs in observers
            obs.update(event)

class ConcreteSubject extends Subject
    state: State
    setState(newState)
        state = newState
        notify(new StateChangedEvent(state))
```

## Example

```
// Player health notifies HUD, audio, and achievement systems on every change

interface HealthObserver
    onHealthChanged(current: Int, max: Int)

class PlayerHealth   // Subject
    current: Int
    max: Int
    observers: List<HealthObserver>

    subscribe(obs)   -> observers.add(obs)
    unsubscribe(obs) -> observers.remove(obs)

    takeDamage(amount)
        current = max(0, current - amount)
        for obs in observers: obs.onHealthChanged(current, max)

class HealthBarUI implements HealthObserver
    onHealthChanged(current, max)
        bar.setFill(current / max)
        bar.setColor(current < max * 0.25 ? RED : GREEN)

class AudioSystem implements HealthObserver
    onHealthChanged(current, max)
        if current < max * 0.2  -> playHeartbeatLoop()
        else                    -> stopHeartbeatLoop()

class AchievementSystem implements HealthObserver
    onHealthChanged(current, max)
        if current == 1 -> unlock("Living on the Edge")

// Wiring
health = new PlayerHealth(100, 100)
health.subscribe(new HealthBarUI())
health.subscribe(new AudioSystem())
health.subscribe(new AchievementSystem())

health.takeDamage(85)   // all three observers react
```

## Trade-offs

+ Loose coupling — subject only knows the Observer interface, not concrete types
+ Observers can be added and removed at runtime without touching the subject
- Observers that forget to unsubscribe create memory leaks and stale callbacks
- Notification cascades can make execution order hard to trace and reason about

## Related

- **Mediator** — replaces direct Observer wiring when coordination logic between subjects grows complex
- **Command** — events passed to observers can be Command objects for deferred execution or logging
- **Publish-Subscribe** — generalizes Observer with a message broker that fully decouples publishers from subscribers
