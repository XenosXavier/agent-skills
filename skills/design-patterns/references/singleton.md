# Singleton

**Intent:** Guarantee exactly one instance of a class exists and provide a single global access point to it.

## Structure

```
class Singleton
    private static instance: Singleton

    private constructor()
        // initialisation logic

    static method getInstance() -> Singleton
        if Singleton.instance is null
            Singleton.instance = new Singleton()
        return Singleton.instance

    method doWork() -> ...

// Thread-safe variant (double-checked locking)
class Singleton
    private static volatile instance: Singleton
    private static lock: Mutex

    static method getInstance() -> Singleton
        if instance is null
            acquire lock
                if instance is null
                    instance = new Singleton()
            release lock
        return instance
```

## Example

```
class AudioManager
    private static instance: AudioManager
    private engine: AudioEngine
    private masterVolume: float = 1.0

    private constructor()
        this.engine = new AudioEngine()   // hardware init — must happen only once
        this.engine.start()

    static method getInstance() -> AudioManager
        if AudioManager.instance is null
            AudioManager.instance = new AudioManager()
        return AudioManager.instance

    method playSound(soundId: string, volume: float)
        this.engine.play(soundId, volume * this.masterVolume)

    method setMasterVolume(volume: float)
        this.masterVolume = volume

// Any part of the game reaches the same instance
AudioManager.getInstance().setMasterVolume(0.5)
AudioManager.getInstance().playSound("sword_clash", 0.8)
AudioManager.getInstance().playSound("footstep", 0.3)
// AudioEngine is initialised exactly once regardless of call count
```

## Trade-offs

+ Enforces the single-instance constraint at the class level
+ Supports lazy initialisation — instance created only on first use
- Introduces global mutable state, making unit tests hard to isolate and reset
- Creates hidden dependencies — callers use getInstance() instead of declaring the dependency explicitly

## Related

- **Abstract Factory / Builder / Prototype** — factory objects are often Singletons since one factory instance is sufficient
- **Facade** — a Facade is sometimes a Singleton when it represents a single subsystem entry point
- **Monostate** — alternative where all instances share static fields instead of enforcing a single instance
