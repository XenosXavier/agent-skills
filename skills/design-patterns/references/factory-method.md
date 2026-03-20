# Factory Method

**Intent:** Delegate object creation to a subclass so the base class never hard-codes which concrete type to instantiate.

## Structure

```
abstract class Creator
    abstract method createProduct() -> Product
    method doWork()
        p = this.createProduct()
        p.use()

class ConcreteCreatorA extends Creator
    method createProduct() -> Product
        return new ConcreteProductA()

class ConcreteCreatorB extends Creator
    method createProduct() -> Product
        return new ConcreteProductB()

interface Product
    method use()
```

## Example

```
abstract class Level
    abstract method spawnEnemy() -> Enemy
    method startWave()
        for i in 1..10
            enemy = this.spawnEnemy()
            enemy.activate()

class ForestLevel extends Level
    method spawnEnemy() -> Enemy
        return new Goblin()

class CastleLevel extends Level
    method spawnEnemy() -> Enemy
        return new Knight()

interface Enemy
    method activate()

class Goblin implements Enemy
    method activate() -> move toward player, attack with sword

class Knight implements Enemy
    method activate() -> patrol corridor, block attacks

// Usage
level = new ForestLevel()
level.startWave()   // spawns 10 Goblins without Level knowing the type
```

## Trade-offs

+ Eliminates tight coupling between creator and concrete product
+ New product types added by subclassing, not modifying existing code
- One new subclass required per product type
- Overkill when only one product type ever exists

## Related

- **Abstract Factory** — groups multiple Factory Methods into one coordinated interface
- **Template Method** — Factory Method is Template Method applied to object creation
- **Prototype** — alternative when cloning an existing instance is cheaper than constructing fresh
