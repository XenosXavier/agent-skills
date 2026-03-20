# Flyweight

**Intent:** Share a small set of immutable objects across thousands of instances by separating intrinsic (shared, immutable) state from extrinsic (per-instance, caller-supplied) state.

## Structure

```
class Flyweight
    private intrinsic: State    // shared, immutable — stored once

    constructor(intrinsic: State)
        this.intrinsic = intrinsic

    method operation(extrinsic: Context)
        doWork(intrinsic, extrinsic)   // combine both

class FlyweightFactory
    private pool: Map<Key, Flyweight>

    method get(key: Key): Flyweight
        if not pool[key]
            pool[key] = new Flyweight(keyToState(key))
        return pool[key]            // always returns cached instance

// Client stores extrinsic state; requests flyweight per operation
factory = new FlyweightFactory()
for each item in largeDataset
    fw = factory.get(item.sharedKey)
    fw.operation(item.uniqueContext)   // extrinsic passed in, not stored
```

## Example

```
// Particle system: 10,000 particles share intrinsic state; each owns only extrinsic state.
// Intrinsic (shared): texture, blendMode, baseColor.
// Extrinsic (per particle): position, velocity, life.

class ParticleType                    // Flyweight — one instance per type
    texture: Texture
    blendMode: BlendMode
    baseColor: Color
    draw(canvas, pos: Vector2, alpha: Float)
        canvas.draw(texture, pos, baseColor.withAlpha(alpha), blend: blendMode)

class ParticleTypeFactory
    cache: Map<String, ParticleType>
    get(name): ParticleType
        cache[name] ?= loadFromConfig(name)
        return cache[name]

class Particle                        // holds only extrinsic state
    position: Vector2
    velocity: Vector2
    life:     Float           // 0.0 – 1.0
    type:     ParticleType    // shared reference
    update(dt)   -> position += velocity * dt; life -= dt * decayRate
    draw(canvas) -> type.draw(canvas, position, alpha: life)

factory  = new ParticleTypeFactory()
fireType = factory.get("fire")        // created once, shared by all fire particles

repeat 10000
    particles.add(new Particle(type: fireType, position: random(), velocity: random(), life: 1.0))
```

## Trade-offs

+ Drastic memory reduction when thousands of objects share most of their state
+ Fewer allocations means less GC pressure in managed runtimes
- Trades memory for CPU: extrinsic state must be passed on every call
- Callers must manage and supply extrinsic state explicitly — more complex call sites

## Related

- **Composite** — Flyweight is commonly applied to leaf nodes in large Composite trees
- **State / Strategy** — often implemented as Flyweights when the objects carry no per-instance state
- **Factory Method / Singleton** — FlyweightFactory acts as a keyed pool, guaranteeing shared instances
