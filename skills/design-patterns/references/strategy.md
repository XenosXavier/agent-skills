# Strategy

**Intent:** Define a family of interchangeable algorithms behind a common interface so the algorithm can be swapped independently of the client that uses it.

## Structure

```
interface Strategy
    execute(data: Data): Result

class ConcreteStrategyA implements Strategy
    execute(data) -> // algorithm A

class ConcreteStrategyB implements Strategy
    execute(data) -> // algorithm B

class Context
    strategy: Strategy

    setStrategy(s: Strategy) -> this.strategy = s
    doWork(data: Data): Result -> return strategy.execute(data)

// Client
ctx = new Context()
ctx.setStrategy(new ConcreteStrategyA())
result = ctx.doWork(myData)

ctx.setStrategy(new ConcreteStrategyB())   // swap at runtime
```

## Example

```
// RTS game: ground units use A*, flyers use straight-line, ships use water-only BFS

interface PathfindingStrategy
    findPath(start: Vector2, goal: Vector2, world: World): Path

class AStarPathfinding implements PathfindingStrategy
    findPath(start, goal, world)
        return astar(start, goal, world.groundGrid)

class StraightLinePathfinding implements PathfindingStrategy
    findPath(start, goal, world)
        return Path.straight(start, goal)   // flying — ignores terrain

class NavalBFS implements PathfindingStrategy
    findPath(start, goal, world)
        return bfs(start, goal, world.waterGrid)

class Unit
    pathfinding: PathfindingStrategy
    position: Vector2

    moveTo(dest: Vector2, world: World)
        path = pathfinding.findPath(position, dest, world)
        followPath(path)

groundSoldier = new Unit(pathfinding: new AStarPathfinding())
fighter       = new Unit(pathfinding: new StraightLinePathfinding())
battleship    = new Unit(pathfinding: new NavalBFS())

// Swap at runtime — soldier boards a boat
groundSoldier.pathfinding = new NavalBFS()
```

## Trade-offs

+ Eliminates conditional logic for choosing behavior — each variant is a clean isolated class
+ Strategies are independently testable and swappable at runtime
- Clients must know which strategies exist in order to select one
- Adds extra classes and indirection for trivially simple or single-variant cases

## Related

- **State** — structurally identical; State drives behavior changes from within the object; Strategy is chosen by the client
- **Template Method** — Template Method fixes algorithm structure via inheritance; Strategy swaps whole algorithms via composition
- **Command** — a Command is often a Strategy with an `execute()` call and optional undo support
