# Decorator

**Intent:** Attach additional responsibilities to an object at runtime by wrapping it, as a flexible alternative to subclassing when behaviors need to be combined in arbitrary permutations.

## Structure

```
interface Component
    method execute(): Result

class ConcreteComponent implements Component
    method execute(): Result
        return coreWork()

class Decorator implements Component       // abstract base wrapper
    protected wrapped: Component

    constructor(c: Component)
        this.wrapped = c

    method execute(): Result
        return wrapped.execute()           // default: pure delegation

class ConcreteDecoratorA extends Decorator
    method execute(): Result
        doSetup()
        result = wrapped.execute()
        doTeardown()
        return result

class ConcreteDecoratorB extends Decorator
    method execute(): Result
        return transform(wrapped.execute())
```

## Example

```
// Game UI text: combine bold, color, and shadow at runtime — no subclass per combo.

interface TextRenderer
    method render(text: String): String

class PlainText implements TextRenderer
    method render(text: String): String
        return text

class BoldDecorator extends TextRenderer
    constructor(inner: TextRenderer)   this.inner = inner
    method render(text: String): String
        return "<b>" + inner.render(text) + "</b>"

class ColorDecorator extends TextRenderer
    constructor(inner: TextRenderer, color: String)
        this.inner = inner
        this.color = color
    method render(text: String): String
        return "<color=" + color + ">" + inner.render(text) + "</color>"

class ShadowDecorator extends TextRenderer
    constructor(inner: TextRenderer)   this.inner = inner
    method render(text: String): String
        base = inner.render(text)
        return renderShadow(base, offset=2) + base

// Compose at runtime — stack order matters
scoreLabel = new ColorDecorator(new BoldDecorator(new PlainText()), "red")
scoreLabel.render("Score: 999")
// => <color=red><b>Score: 999</b></color>

tooltip = new ShadowDecorator(new PlainText())
tooltip.render("Press E to interact")
```

## Trade-offs

+ Behaviors composed at runtime without touching existing classes
+ Each decorator does one thing — clean single responsibility
- Wrapping stack makes debugging and stack traces hard to follow
- Object identity breaks: a decorated object is not `==` its inner object

## Related

- **Adapter** — changes an interface; Decorator preserves the interface while adding behavior
- **Composite** — also recursive composition, but builds a multi-child tree; Decorator wraps exactly one object
- **Strategy** — changes internal algorithm; Decorator wraps from the outside without touching internals
- **Chain of Responsibility** — similar chaining, but each handler decides whether to pass on; Decorators always delegate
