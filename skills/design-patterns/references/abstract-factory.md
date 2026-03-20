# Abstract Factory

**Intent:** Create families of related objects through a single interface, guaranteeing that products from different families are never accidentally mixed.

## Structure

```
interface AbstractFactory
    method createProductA() -> AbstractProductA
    method createProductB() -> AbstractProductB

class FamilyOneFactory implements AbstractFactory
    method createProductA() -> AbstractProductA
        return new ProductA1()
    method createProductB() -> AbstractProductB
        return new ProductB1()

class FamilyTwoFactory implements AbstractFactory
    method createProductA() -> AbstractProductA
        return new ProductA2()
    method createProductB() -> AbstractProductB
        return new ProductB2()

class Client
    constructor(factory: AbstractFactory)
        this.a = factory.createProductA()
        this.b = factory.createProductB()
```

## Example

```
interface ThemeFactory
    method createHUD()    -> HUD
    method createMenu()   -> Menu
    method createCursor() -> Cursor

class FantasyThemeFactory implements ThemeFactory
    method createHUD()    -> HUD    return new ParchmentHUD()
    method createMenu()   -> Menu   return new WoodCarvedMenu()
    method createCursor() -> Cursor return new MagicWandCursor()

class SciFiThemeFactory implements ThemeFactory
    method createHUD()    -> HUD    return new HolographicHUD()
    method createMenu()   -> Menu   return new NeonGlassMenu()
    method createCursor() -> Cursor return new LaserBeamCursor()

class GameUI
    constructor(factory: ThemeFactory)
        this.hud    = factory.createHUD()
        this.menu   = factory.createMenu()
        this.cursor = factory.createCursor()
    method draw()
        this.hud.draw(); this.menu.draw(); this.cursor.draw()

// Usage — swap entire theme by swapping the factory
factory = config.theme == "scifi"
    ? new SciFiThemeFactory()
    : new FantasyThemeFactory()
ui = new GameUI(factory)
ui.draw()
```

## Trade-offs

+ Guarantees products from the same family are always used together
+ Swapping an entire product family is a single-line change
- Adding a new product type requires updating every factory interface and all implementations
- More classes and interfaces than simpler alternatives

## Related

- **Factory Method** — Abstract Factory is typically a set of Factory Methods, one per product type
- **Singleton** — concrete factory instances are often Singletons
- **Prototype** — a factory can store prototypes and clone them instead of constructing via subclasses
