# Event Sourcing

**Intent:** Persists state as an immutable sequence of domain events instead of current values, so state is always derived by replaying those events.

## Structure

```
// Immutable event value objects
class OrderPlacedEvent     { orderId, customerId, items, total, placedAt }
class OrderCancelledEvent  { orderId, reason, cancelledAt }

// Aggregate: apply events to rebuild state; raise events for mutations
class Order
    id, status, total

    apply(OrderPlacedEvent e)    -> id = e.orderId; status = "placed"; total = e.total
    apply(OrderCancelledEvent e) -> status = "cancelled"

    cancel(reason)
        if status == "cancelled" -> throw AlreadyCancelledError
        raise OrderCancelledEvent(id, reason, now())

// Event-sourced repository
class OrderRepository
    findById(id)
        events = eventStore.loadStream("order-" + id)
        order  = new Order()
        events.forEach(e => order.apply(e))
        return order

    save(order)
        eventStore.appendToStream("order-" + order.id, order.pullUncommittedEvents())

// EventStore interface
interface EventStore
    loadStream(streamId) -> List<DomainEvent>
    appendToStream(streamId, events, expectedVersion?) -> void
    subscribeToAll(handler) -> void
```

## Example

```
// Bank account — every transaction is an event; balance is always derivable

class AccountOpenedEvent  { accountId, ownerId, openedAt }
class MoneyDepositedEvent { accountId, amount, depositedAt }
class MoneyWithdrawnEvent { accountId, amount, withdrawnAt }
class AccountFrozenEvent  { accountId, reason, frozenAt }

class BankAccount
    id, balance, status

    static open(ownerId)
        acc = new BankAccount()
        acc.raise(AccountOpenedEvent(newId(), ownerId, now()))
        return acc

    deposit(amount)
        if amount <= 0 -> throw InvalidAmountError
        raise MoneyDepositedEvent(id, amount, now())

    withdraw(amount)
        if status == "frozen"      -> throw AccountFrozenError
        if balance - amount < 0    -> throw InsufficientFundsError
        raise MoneyWithdrawnEvent(id, amount, now())

    // Pure state reconstruction — no validation here
    apply(AccountOpenedEvent e)  -> balance = 0; status = "active"
    apply(MoneyDepositedEvent e) -> balance += e.amount
    apply(MoneyWithdrawnEvent e) -> balance -= e.amount
    apply(AccountFrozenEvent e)  -> status = "frozen"

// Temporal query: balance at any past moment
function balanceAt(accountId, timestamp)
    events = eventStore.loadStream("account-" + accountId)
    acc    = new BankAccount()
    events.filter(e => e.occurredAt <= timestamp).forEach(e => acc.apply(e))
    return acc.balance

// Projector building a transaction-history read model
class TransactionHistoryProjector
    on(MoneyDepositedEvent e) -> readDb.insert("transactions", { type: "credit", amount: e.amount, at: e.depositedAt })
    on(MoneyWithdrawnEvent e) -> readDb.insert("transactions", { type: "debit",  amount: e.amount, at: e.withdrawnAt })
```

## Trade-offs

+ Complete, immutable audit log — every state change is permanently recorded
+ Temporal queries: reconstruct state at any past point by replaying up to that moment
+ New read models can be built retroactively by projecting existing events
- Event schema evolution is hard; old formats must be handled forever (upcasting/versioning)
- Long streams are slow without snapshots; snapshot strategy adds moving parts
- Truly immutable stores conflict with GDPR "right to be forgotten"

## Related

- **CQRS** — almost always paired; the event stream feeds projectors that build CQRS read models
- **Snapshot** — optimisation that checkpoints aggregate state to avoid full-stream replay
- **Repository** — adapted to load aggregates by replaying a stream and save by appending new events
- **Saga** — reacts to events to coordinate long-running multi-aggregate workflows
