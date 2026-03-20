# Proxy

**Intent:** Provide a same-interface surrogate for another object to control access — enabling lazy init, access control, caching, or logging without the client knowing.

## Structure

```
interface Subject
    method request(): Result

class RealSubject implements Subject
    method request(): Result
        return doRealWork()

class Proxy implements Subject
    private real: RealSubject = null   // lazy — created on first use

    method request(): Result
        if not authorized(): throw AccessDenied
        if real == null
            real = new RealSubject()   // virtual proxy: defer creation
        log("request start")
        result = real.request()
        log("request end")
        return result
```

## Example

```
// Virtual proxy: 200 terrain textures registered instantly; disk load deferred to first render.

interface Texture
    bind(slot: Int)
    getSize(): Vector2

class GPUTexture implements Texture       // RealSubject — expensive to create
    constructor(path)
        data   = loadFileFromDisk(path)   // blocking I/O
        handle = GPU.upload(data)
        size   = data.dimensions
    bind(slot)  -> GPU.bindTexture(handle, slot)
    getSize()   -> return size

class LazyTexture implements Texture      // Virtual Proxy — cheap to create
    path: String
    real: GPUTexture = null
    ensureLoaded()  -> if real == null: real = new GPUTexture(path)
    bind(slot)      -> ensureLoaded(); real.bind(slot)
    getSize()       -> ensureLoaded(); return real.getSize()

// Startup: zero disk reads
tiles = terrainMap.map(cfg => new LazyTexture(cfg.path))

// Render: only visible tiles trigger a load
for tile in visibleTiles: tile.bind(0)
```

```
// Protection proxy: guard admin-only writes, pass through reads
class AdminServiceProxy implements UserService
    constructor(real, currentUser)
    deleteUser(id)
        if not currentUser.hasRole("ADMIN") -> throw UnauthorizedException
        real.deleteUser(id)
    getUser(id) -> real.getUser(id)    // unrestricted
```

## Trade-offs

+ Controls or defers access transparently — client code unchanged
+ Cross-cutting concerns (auth, caching, logging) added without touching RealSubject
- Extra indirection on every call; virtual proxies can cause latency spikes on first access
- Must forward all interface methods — keeping proxy in sync with subject requires discipline

## Related

- **Decorator** — same wrapping structure, but adds behavior; Proxy controls or restricts access
- **Adapter** — changes the interface; Proxy keeps exactly the same interface as its subject
- **Facade** — simplifies a whole subsystem; Proxy stands in for a single object
- **Flyweight** — also optimizes object usage, but via sharing; Proxy optimizes via deferred creation or caching
