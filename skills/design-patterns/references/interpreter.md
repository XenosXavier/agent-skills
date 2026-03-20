# Interpreter

**Intent:** Represent a simple language's grammar as a class hierarchy and evaluate sentences by walking the resulting expression tree.

## Structure

```
interface Expression
    interpret(context: Context): Value

class TerminalExpression implements Expression
    value: Literal
    interpret(ctx) -> return value  // or look up in ctx

class NonterminalExpression implements Expression
    left: Expression
    right: Expression
    interpret(ctx)
        return combine(left.interpret(ctx), right.interpret(ctx))

class Context
    variables: Map<String, Value>
    get(name): Value
    set(name, value)
```

## Example

```
// RPG stat formula evaluator: designers write "base + level * multiplier" in config

class NumberExpr implements Expression
    value: Float
    interpret(ctx) -> return value

class VariableExpr implements Expression
    name: String
    interpret(ctx) -> return ctx.get(name)

class AddExpr implements Expression
    left, right: Expression
    interpret(ctx) -> return left.interpret(ctx) + right.interpret(ctx)

class MultiplyExpr implements Expression
    left, right: Expression
    interpret(ctx) -> return left.interpret(ctx) * right.interpret(ctx)

// AST for: base + level * multiplier
formula = new AddExpr(
    new VariableExpr("base"),
    new MultiplyExpr(
        new VariableExpr("level"),
        new VariableExpr("multiplier")
    )
)

ctx = new Context()
ctx.set("base", 100)
ctx.set("level", 5)
ctx.set("multiplier", 12)

damage = formula.interpret(ctx)   // 100 + 5 * 12 = 160
```

## Trade-offs

+ Each grammar rule maps to one class — easy to extend with new expression types
+ New operations (pretty-print, compile) can be added via Visitor without touching expression classes
- One class per grammar rule — complex grammars produce unmanageable class counts
- Tree-walking is slow; deep trees risk stack overflows on large inputs

## Related

- **Composite** — the AST is a Composite tree; NonterminalExpression is the composite node
- **Visitor** — use Visitor to add new operations (type-checking, codegen) over the AST
- **Flyweight** — shared terminal symbols can be flyweights to reduce memory
