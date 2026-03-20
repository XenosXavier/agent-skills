# Prototype

**Intent:** Create new objects by cloning an existing configured instance instead of constructing from scratch, avoiding repeated expensive initialisation.

## Structure

```
interface Prototype
    method clone() -> Prototype

class ConcretePrototype implements Prototype
    field data: ComplexObject

    constructor copy(source: ConcretePrototype)
        this.data = source.data.clone()   // deep copy nested objects

    method clone() -> Prototype
        return new ConcretePrototype copy(this)

// Optional registry
class PrototypeRegistry
    private cache: map<string, Prototype>
    method register(key: string, proto: Prototype)
        cache[key] = proto
    method get(key: string) -> Prototype
        return cache[key].clone()
```

## Example

```
interface Entity
    method clone() -> Entity
    method activate(x: int, y: int)

class Enemy implements Entity
    field spritesheet: Spritesheet   // expensive to load from disk
    field stats: StatBlock           // complex nested object
    field aiProfile: AIProfile       // read-only, safe to share

    constructor(type: string)
        this.spritesheet = AssetLoader.load(type + ".png")  // slow
        this.stats       = StatBlock.fromConfig(type)
        this.aiProfile   = AIProfile.fromConfig(type)

    constructor copy(source: Enemy)
        this.spritesheet = source.spritesheet    // shared ref — read-only asset
        this.stats       = source.stats.clone()  // deep copy — each enemy owns stats
        this.aiProfile   = source.aiProfile      // shared ref — read-only

    method clone() -> Entity
        return new Enemy copy(this)

    method activate(x, y)
        this.position = (x, y)
        this.state    = ALIVE

// Setup once at load time
registry = new PrototypeRegistry()
registry.register("goblin", new Enemy("goblin"))
registry.register("knight", new Enemy("knight"))

// Spawn fast — no asset reloading
goblin1 = registry.get("goblin") as Enemy
goblin1.activate(10, 20)

goblin2 = registry.get("goblin") as Enemy   // instant second clone
goblin2.activate(15, 30)
```

## Trade-offs

+ Cloning is far faster than re-running expensive construction for each instance
+ New variants registered at runtime without changing code
- Implementing clone() correctly is hard when the object graph is deep or has circular references
- Cloning bypasses constructors — any constructor-side-effects are silently skipped

## Related

- **Abstract Factory** — can use Prototype internally; factory stores prototypes and clones instead of subclassing
- **Memento** — Prototype can snapshot object state similarly, but without a dedicated caretaker
- **Flyweight** — both reduce cost of many similar objects; Flyweight shares state, Prototype copies it
