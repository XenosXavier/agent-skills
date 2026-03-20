# Adapter

**Intent:** Translate an incompatible interface into the interface a client expects, without modifying either side.

## Structure

```
interface Target
    method request(): Result

class Adaptee                          // existing class, cannot modify
    method specificRequest(): RawData

class Adapter implements Target
    private adaptee: Adaptee

    constructor(adaptee: Adaptee)
        this.adaptee = adaptee

    method request(): Result
        raw = adaptee.specificRequest()
        return transform(raw)          // translate here

client.use(new Adapter(new Adaptee()))
```

## Example

```
// Game engine expects InputDevice.getAxis(name)
// Legacy SDK only exposes readRawX() / readRawY()

interface InputDevice
    method getAxis(name: String): Float   // "horizontal" | "vertical"

class OldJoystick                         // third-party, immutable
    method readRawX(): Int                // range: -32768..32767
    method readRawY(): Int

class JoystickAdapter implements InputDevice
    private joy: OldJoystick

    method getAxis(name: String): Float
        if name == "horizontal" return joy.readRawX() / 32767.0
        if name == "vertical"   return joy.readRawY() / 32767.0
        return 0.0

// Game code is unaware of OldJoystick
player.setInput(new JoystickAdapter(new OldJoystick()))
input = player.getInput()
dx = input.getAxis("horizontal")   // -1.0 .. 1.0
```

## Trade-offs

+ Reuses existing/third-party code with zero modification
+ Isolates all translation logic in one class — easy to swap
- Extra indirection on every call; avoid in hot paths
- Hides true capabilities or limitations of the underlying class

## Related

- **Bridge** — designed upfront to separate two hierarchies; Adapter retrofits mismatched interfaces
- **Decorator** — same wrapping structure, but adds behavior instead of translating an interface
- **Proxy** — keeps the same interface as its subject; Adapter intentionally changes it
- **Facade** — simplifies many classes into one interface; Adapter translates one interface to another
