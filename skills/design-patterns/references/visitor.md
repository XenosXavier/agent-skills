# Visitor

**Intent:** Add new operations to a stable object structure without modifying its element classes, using double dispatch to route calls.

## Structure

```
interface Visitor
    visitA(element: ConcreteElementA)
    visitB(element: ConcreteElementB)

interface Element
    accept(visitor: Visitor)

class ConcreteElementA implements Element
    data: DataA
    accept(visitor) -> visitor.visitA(this)   // double dispatch

class ConcreteElementB implements Element
    data: DataB
    accept(visitor) -> visitor.visitB(this)

class ConcreteVisitor implements Visitor
    visitA(element) -> // operation on element.data
    visitB(element) -> // operation on element.data

// Traversal
for each element in structure
    element.accept(visitor)
```

## Example

```
// Game scene graph: multiple rendering passes traverse the same nodes
// without adding render logic into node classes

interface SceneVisitor
    visitMesh(node: MeshNode)
    visitLight(node: LightNode)
    visitCamera(node: CameraNode)
    visitParticle(node: ParticleNode)

interface SceneNode
    accept(visitor: SceneVisitor)

class MeshNode implements SceneNode
    mesh: Mesh
    material: Material
    transform: Matrix4
    accept(visitor) -> visitor.visitMesh(this)

class LightNode implements SceneNode
    color: Color
    intensity: Float
    type: LightType
    accept(visitor) -> visitor.visitLight(this)

class ShadowMapVisitor implements SceneVisitor
    visitMesh(node)     -> shadowMap.renderDepth(node.mesh, node.transform)
    visitLight(node)    -> if node.type == DIRECTIONAL: shadowMap.setProjection(node)
    visitCamera(node)   -> // ignored for shadow pass
    visitParticle(node) -> // particles don't cast shadows

class DebugOverlayVisitor implements SceneVisitor
    visitMesh(node)     -> debugDraw.wireBounds(node.mesh.bounds, node.transform)
    visitLight(node)    -> debugDraw.icon(LightIcon, node.position)
    visitCamera(node)   -> debugDraw.frustum(node.frustum)
    visitParticle(node) -> debugDraw.count(node.system.aliveCount, node.position)

scene: List<SceneNode> = buildScene()

for each node in scene: node.accept(new ShadowMapVisitor())
for each node in scene: node.accept(new DebugOverlayVisitor())
```

## Trade-offs

+ Adding a new operation means a new Visitor class — zero changes to element classes
+ Related per-type logic is consolidated in one class rather than scattered across elements
- Adding a new element type requires updating every existing Visitor
- May break encapsulation — Visitors often need access to element internals

## Related

- **Composite** — Visitor is most useful when the object structure is a Composite tree
- **Iterator** — Iterator drives traversal; Visitor defines what to do at each element
- **Interpreter** — adding operations to an AST without modifying expression classes is a canonical Visitor use case
