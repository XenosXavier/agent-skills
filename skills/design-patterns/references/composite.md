# Composite

**Intent:** Compose objects into tree structures so clients can treat individual objects and groups of objects uniformly through one interface.

## Structure

```
interface Component
    method operation(): Result

class Leaf implements Component
    method operation(): Result
        return doLeafWork()

class Composite implements Component
    private children: List<Component>

    method add(c: Component)
        children.add(c)

    method remove(c: Component)
        children.remove(c)

    method operation(): Result
        result = initialValue
        for each child in children
            result = combine(result, child.operation())
        return result

// Client treats both identically
root: Component = new Composite()
root.add(new Leaf())
root.add(new Composite())   // nesting is transparent
root.operation()
```

## Example

```
// 2D scene graph: renderer calls draw() on root; the call cascades automatically.

interface SceneNode
    method draw(canvas: Canvas)
    method getBounds(): Rect

class Sprite implements SceneNode         // Leaf
    private texture: Texture
    private position: Vector2

    method draw(canvas)
        canvas.drawTexture(texture, position)

    method getBounds(): Rect
        return Rect(position, texture.size)

class SceneGroup implements SceneNode     // Composite
    private children: List<SceneNode>
    private transform: Matrix

    method add(node: SceneNode)   children.add(node)

    method draw(canvas)
        canvas.pushTransform(transform)
        for each child in children
            child.draw(canvas)            // leaf or group — same call
        canvas.popTransform()

    method getBounds(): Rect
        return unionOf(child.getBounds() for each child)

// Build the scene
hud = new SceneGroup()
hud.add(new Sprite(healthBarTex))
hud.add(new Sprite(ammoIconTex))

world = new SceneGroup()
world.add(new Sprite(playerTex))
world.add(new Sprite(enemyTex))
world.add(hud)                            // group inside group

world.draw(canvas)                        // draws everything recursively
```

## Trade-offs

+ Single call on root propagates to entire tree — client code stays simple
+ New leaf and composite types slot in without touching existing code
- Shared interface may force meaningless methods on leaves (e.g., `add()` on Sprite)
- Deep trees produce deep call stacks; hard to restrict child types per composite

## Related

- **Iterator** — traverse a Composite tree without exposing its internal structure
- **Visitor** — add new operations to a tree without modifying node classes
- **Decorator** — also recursive composition, but wraps a single object rather than building a tree
- **Flyweight** — apply to leaf nodes in large trees to reduce per-node memory cost
