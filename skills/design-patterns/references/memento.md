# Memento

**Intent:** Capture an object's internal state in an opaque snapshot so it can be restored later without breaking encapsulation.

## Structure

```
class Memento
    state: OriginatorState   // private — only Originator reads it

class Originator
    currentState: OriginatorState

    save(): Memento
        return new Memento(copy of currentState)

    restore(m: Memento)
        currentState = m.state

class Caretaker
    history: Stack<Memento>
    originator: Originator

    doAndSave(action)
        history.push(originator.save())
        action()

    undo()
        if history not empty
            originator.restore(history.pop())
```

## Example

```
// Text editor with unlimited undo/redo via snapshot stack

class EditorSnapshot   // Memento
    text: String
    cursorPos: Int
    selectionRange: Range

class TextEditor   // Originator
    text: String
    cursorPos: Int
    selectionRange: Range

    save(): EditorSnapshot
        return new EditorSnapshot(text, cursorPos, selectionRange)

    restore(snap: EditorSnapshot)
        text           = snap.text
        cursorPos      = snap.cursorPos
        selectionRange = snap.selectionRange

    type(chars)
        text.insert(cursorPos, chars)
        cursorPos += chars.length

class EditHistory   // Caretaker
    editor: TextEditor
    undoStack: Stack<EditorSnapshot>
    redoStack: Stack<EditorSnapshot>

    execute(action)
        undoStack.push(editor.save())
        redoStack.clear()
        action()

    undo()
        redoStack.push(editor.save())
        editor.restore(undoStack.pop())

    redo()
        undoStack.push(editor.save())
        editor.restore(redoStack.pop())

history.execute(() -> editor.type("Hello"))
history.execute(() -> editor.type(" World"))
history.undo()   // back to "Hello"
history.redo()   // "Hello World" again
```

## Trade-offs

+ Preserves encapsulation — Caretaker handles opaque tokens and never inspects contents
+ Simple undo/redo: just push/pop snapshots on a stack
- Full state copies are expensive when the Originator holds a large object graph
- Caretaker must manage Memento lifetime or memory grows unbounded

## Related

- **Command** — Commands often store a Memento internally to support undo
- **Prototype** — cloning the Originator is an alternative when the full object is the snapshot
- **Iterator** — an Iterator can save its position in a Memento for bookmark-style rollback
