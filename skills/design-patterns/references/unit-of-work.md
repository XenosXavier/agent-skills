# Unit of Work

**Intent:** Tracks all entity changes made during a business operation and flushes them to the database as a single atomic transaction.

## Structure

```
interface UnitOfWork
    registerNew(entity) -> void
    registerDirty(entity) -> void
    registerDeleted(entity) -> void
    commit() -> void
    rollback() -> void

class DbUnitOfWork implements UnitOfWork
    newEntities, dirtyEntities, deletedEntities: List<Entity>
    transaction: DbTransaction = db.beginTransaction()

    registerNew(entity)     -> newEntities.add(entity)
    registerDirty(entity)   -> dirtyEntities.add(entity)
    registerDeleted(entity) -> deletedEntities.add(entity)

    commit()
        try
            newEntities.forEach(e     => insertToDb(e))
            dirtyEntities.forEach(e   => updateInDb(e))
            deletedEntities.forEach(e => deleteFromDb(e))
            transaction.commit()
        catch -> transaction.rollback(); throw

class OrderRepository
    constructor(uow: UnitOfWork)
    add(order)    -> uow.registerNew(order)
    update(order) -> uow.registerDirty(order)
```

## Example

```
// E-commerce checkout — order, inventory, and payment must all commit together
class CheckoutService
    constructor(uow: UnitOfWork,
                orderRepo: OrderRepository,
                inventoryRepo: InventoryRepository,
                paymentRepo: PaymentRepository)

    placeOrder(customerId, cartItems, paymentDetails)
        order = Order.create(customerId, cartItems)
        orderRepo.add(order)                      // registerNew — no DB write yet

        for each item in cartItems
            inv = inventoryRepo.findById(item.productId)
            inv.quantity -= item.quantity
            inventoryRepo.update(inv)             // registerDirty — no DB write yet

        payment = Payment.create(order.id, order.total, paymentDetails)
        paymentRepo.add(payment)                  // registerNew — no DB write yet

        uow.commit()                              // single atomic flush
        return order

// Wiring per request
uow     = DbUnitOfWork(db)
service = CheckoutService(uow, OrderRepository(uow), InventoryRepository(uow), PaymentRepository(uow))
// If any step throws, rollback fires — no orphaned order or corrupted inventory
```

## Trade-offs

+ Guarantees atomicity across multiple entities without per-service transaction code
+ Batches all writes into one DB round-trip
+ Prevents double-writes when the same entity is modified multiple times before flush
- Developers must remember to call `commit()`; forgetting silently drops all changes
- Holding a transaction open for a long Unit of Work increases lock contention
- Many ORMs already implement this (EF `DbContext`, Hibernate `Session`) — wrapping adds redundant boilerplate

## Related

- **Repository** — repositories register changes with the UoW instead of saving immediately; nearly always used together
- **Domain Events** — dispatch events inside `commit()` after persistence succeeds, ensuring events only fire on success
- **Saga** — distributed alternative for coordinating multi-service transactions without a shared database transaction
