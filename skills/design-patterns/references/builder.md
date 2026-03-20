# Builder

**Intent:** Construct a complex object step by step, separating the construction recipe from the object's representation so the same steps can produce different results.

## Structure

```
interface Builder
    method stepA(value)
    method stepB(value)
    method stepC(value)

class ConcreteBuilderX implements Builder
    private product: ProductX
    method reset()        this.product = new ProductX()
    method stepA(value)   this.product.partA = value
    method stepB(value)   this.product.partB = value
    method stepC(value)   this.product.partC = value
    method getResult() -> ProductX
        return this.product

class Director
    method buildVariantOne(builder: Builder)
        builder.stepA("basic"); builder.stepB("standard")
    method buildVariantTwo(builder: Builder)
        builder.stepA("premium"); builder.stepB("deluxe"); builder.stepC("extra")
```

## Example

```
// RPG character: Director owns construction recipes; Builder owns the product.

interface CharacterBuilder
    setClass(name), equipWeapon(w), equipArmor(a), assignSkills(s)

class WarriorBuilder implements CharacterBuilder
    character: Character = new Character()
    setClass(name)  -> character.class  = name
    equipWeapon(w)  -> character.weapon = w
    equipArmor(a)   -> character.armor  = a
    assignSkills(s) -> character.skills = s
    getResult()     -> return character

class CharacterDirector
    buildStarter(b: CharacterBuilder)
        b.setClass("Starter"); b.equipWeapon("Dagger"); b.assignSkills(["Basic Attack"])
    buildElite(b: CharacterBuilder)
        b.setClass("Elite"); b.equipWeapon("Legendary Sword")
        b.equipArmor("Dragon Scale"); b.assignSkills(["Whirlwind", "Battle Cry"])

b = new WarriorBuilder()
new CharacterDirector().buildElite(b)
player = b.getResult()
```

## Trade-offs

+ Eliminates telescoping constructors — each step is explicit and named
+ Director encapsulates reusable construction recipes; builders are swappable
- More classes than a simple constructor or static factory method
- Builder interface must anticipate all steps upfront; extending it later can break existing builders

## Related

- **Abstract Factory** — also constructs complex objects, but in one step across a family; Builder works step by step on one product
- **Composite** — Builder is commonly used to assemble Composite trees
- **Fluent Interface** — modern builders often return `this` from each step to enable method chaining
