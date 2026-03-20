# Facade

**Intent:** Provide a single simplified entry point into a complex subsystem, reducing the number of classes clients need to interact with.

## Structure

```
class SubsystemA
    method a1()
    method a2()

class SubsystemB
    method b1()
    method b2()

class SubsystemC
    method c1()

class Facade
    private a: SubsystemA
    private b: SubsystemB
    private c: SubsystemC

    constructor()
        a = new SubsystemA()
        b = new SubsystemB()
        c = new SubsystemC()

    method operationX()      // orchestrates a1 + b1 + c1
        a.a1(); b.b1(); c.c1()

    method operationY()      // orchestrates a2 + b2
        a.a2(); b.b2()

// Client only imports Facade
facade = new Facade()
facade.operationX()
```

## Example

```
// Game audio: AudioEngine + AssetLoader + Mixer + EffectsChain → two methods for most callers.

class AudioFacade
    private engine:  AudioEngine
    private loader:  AssetLoader
    private mixer:   Mixer
    private effects: EffectsChain

    constructor()
        engine.init(44100, 512)
        sfxCh   = mixer.createChannel("sfx")
        musicCh = mixer.createChannel("music")
        effects.addReverb(sfxCh, 0.2)

    playSfx(path)
        engine.submitBuffer(mixer.mix([loader.loadWav(path)]))

    playMusic(path, vol)
        mixer.setVolume(musicCh, vol)
        engine.submitBuffer(mixer.mix([loader.loadOgg(path)]))

    shutdown() -> engine.shutdown()

audio = new AudioFacade()
audio.playSfx("explosion.wav")
audio.playMusic("theme.ogg", 0.8)
```

## Trade-offs

+ Dramatically reduces coupling between client code and subsystem internals
+ Subsystem internals can change without breaking callers
- Facade can grow into a god object if it absorbs too much orchestration logic
- Thin facades that proxy every method 1-to-1 add indirection with no real value

## Related

- **Adapter** — translates one interface to another; Facade defines a new simpler interface over multiple classes
- **Mediator** — mediates between subsystem components to remove their direct coupling; Facade only simplifies outside access
- **Singleton** — Facades are often singletons since one instance per subsystem is sufficient
