# Command

**Intent:** Encapsulate a request as an object so it can be queued, logged, and undone without the invoker knowing what the operation does.

## Structure

```
interface Command
    execute()
    undo()

class ConcreteCommand implements Command
    receiver: Receiver
    savedState: State

    execute()
        savedState = receiver.currentState
        receiver.doAction()

    undo()
        receiver.setState(savedState)

class Invoker
    history: Stack<Command>

    run(cmd: Command)
        cmd.execute()
        history.push(cmd)

    undoLast()
        if history not empty
            history.pop().undo()
```

## Example

```
// Level editor: every map edit is a command; designer can freely undo/redo

class PlaceTileCommand implements Command
    map: Map
    position: Vector2
    newTile: Tile
    previousTile: Tile

    execute()
        previousTile = map.getTile(position)
        map.setTile(position, newTile)

    undo()
        map.setTile(position, previousTile)

class DeleteEntityCommand implements Command
    scene: Scene
    entity: Entity

    execute()  -> scene.remove(entity)
    undo()     -> scene.add(entity)

class LevelEditor       // Invoker
    history: Stack<Command>
    redoStack: Stack<Command>

    execute(cmd)
        cmd.execute()
        history.push(cmd)
        redoStack.clear()

    undo()
        cmd = history.pop()
        cmd.undo()
        redoStack.push(cmd)

    redo()
        cmd = redoStack.pop()
        cmd.execute()
        history.push(cmd)

editor.execute(new PlaceTileCommand(map, (3,4), GrassTile))
editor.execute(new DeleteEntityCommand(scene, dragon))
editor.undo()   // dragon is back
```

## Trade-offs

+ Undo/redo is straightforward — keep a history stack of Command objects
+ Commands are first-class objects: storable, serializable, and loggable
- One class per operation — large feature sets produce many small command classes
- Undo logic grows complex when commands interact with shared mutable state

## Related

- **Memento** — store a Memento snapshot inside a Command to support richer undo
- **Chain of Responsibility** — commands can travel along a handler chain
- **Composite** — a MacroCommand is a Composite of Commands
