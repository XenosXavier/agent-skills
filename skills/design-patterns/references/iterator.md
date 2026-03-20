# Iterator

**Intent:** Provide a uniform way to traverse a collection's elements sequentially without exposing its internal representation.

## Structure

```
interface Iterator<T>
    hasNext(): Boolean
    next(): T

interface Aggregate<T>
    createIterator(): Iterator<T>

class ConcreteAggregate<T> implements Aggregate<T>
    items: T[]
    createIterator() -> return new ConcreteIterator(this)

class ConcreteIterator<T> implements Iterator<T>
    collection: ConcreteAggregate<T>
    index: Int = 0

    hasNext() -> return index < collection.items.length
    next()
        item = collection.items[index]
        index++
        return item
```

## Example

```
// Turn-based game: AI and render systems iterate active entities
// without knowing the internal sparse-array storage

class EntityManager implements Aggregate<Entity>
    slots: Entity[]   // sparse — nulls where entities were removed

    createIterator() -> return new ActiveEntityIterator(slots)

class ActiveEntityIterator implements Iterator<Entity>
    slots: Entity[]
    index: Int = 0

    hasNext(): Boolean
        while index < slots.length and slots[index] == null
            index++        // skip empty slots
        return index < slots.length

    next(): Entity
        entity = slots[index]
        index++
        return entity

// AI system — unaware of sparse storage
iter = entityManager.createIterator()
while iter.hasNext()
    iter.next().aiComponent.tick()

// Render system runs its own independent cursor
renderIter = entityManager.createIterator()
while renderIter.hasNext()
    renderIter.next().draw()
```

## Trade-offs

+ Multiple independent traversals can run concurrently on the same collection
+ New traversal strategies (reverse, filtered) can be added without touching the collection
- Overkill for plain arrays where a direct index loop is clearer
- Iterating while modifying the collection leads to undefined behavior

## Related

- **Factory Method** — `createIterator()` on the Aggregate is a Factory Method
- **Composite** — Iterators are commonly used to traverse Composite trees
- **Memento** — an Iterator can store its position in a Memento for bookmark-style rollback
