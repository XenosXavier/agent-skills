# Bridge

**Intent:** Decouple an abstraction from its implementation so both can vary independently, eliminating the subclass explosion that results from combining two orthogonal dimensions.

## Structure

```
interface Implementor
    method operationImpl()

class ConcreteImplA implements Implementor
    method operationImpl()   // platform A

class ConcreteImplB implements Implementor
    method operationImpl()   // platform B

class Abstraction
    protected impl: Implementor

    constructor(impl: Implementor)
        this.impl = impl

    method operation()
        impl.operationImpl()

class RefinedAbstraction extends Abstraction
    method operation()
        doExtraWork()
        impl.operationImpl()
```

## Example

```
// UI widgets must render on both OpenGL and DirectX.
// Without Bridge: OpenGLButton, DirectXButton, OpenGLCheckbox, DirectXCheckbox...
// With Bridge: 2 widgets + 2 renderers = 4 classes instead of 4 per combo.

interface Renderer
    method drawRect(x, y, w, h)
    method drawText(x, y, text)

class OpenGLRenderer implements Renderer
    method drawRect(x, y, w, h)   // glBegin / glVertex calls
    method drawText(x, y, text)   // bitmap font via GL

class DirectXRenderer implements Renderer
    method drawRect(x, y, w, h)   // ID3D11DeviceContext calls
    method drawText(x, y, text)   // DirectWrite

class UIWidget
    protected renderer: Renderer
    constructor(renderer: Renderer)
        this.renderer = renderer

class Button extends UIWidget
    method render()
        renderer.drawRect(x, y, width, height)
        renderer.drawText(x + 4, y + 4, label)

class Checkbox extends UIWidget
    method render()
        renderer.drawRect(x, y, size, size)
        if checked: renderer.drawText(x + 2, y + 2, "X")

// Mix freely; swap renderer at runtime
btn = new Button(new OpenGLRenderer())
chk = new Checkbox(new DirectXRenderer())
btn.renderer = new DirectXRenderer()   // runtime switch — no class change
```

## Trade-offs

+ Eliminates combinatorial subclass explosion across two dimensions
+ Abstraction and implementation extend independently
- Extra indirection on every call; two hierarchies to trace during debugging
- Requires identifying the two independent dimensions upfront, which may not be obvious early

## Related

- **Adapter** — retrofits compatibility onto mismatched existing classes; Bridge is designed upfront
- **Abstract Factory** — can create and pair Bridge abstractions with their correct implementations
- **Strategy** — similar object-holds-reference structure, but varies one object's algorithm; Bridge decouples an entire hierarchy
