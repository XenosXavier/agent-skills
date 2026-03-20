# CQRS (Command Query Responsibility Segregation)

**Intent:** Separates write operations (commands) from read operations (queries) so each side can be modelled, optimised, and scaled independently.

## Structure

```
// Write side — enforces business rules, persists via domain model
class PlaceOrderCommand
    customerId, items, shippingAddress

class PlaceOrderHandler
    constructor(orderRepo: OrderRepository, eventBus: EventBus)
    handle(cmd: PlaceOrderCommand)
        order = Order.create(cmd.customerId, cmd.items, cmd.shippingAddress)
        orderRepo.save(order)
        eventBus.publish(OrderPlacedEvent(order))

// Read side — returns data, no side effects
class GetOrderSummaryQuery
    orderId

class GetOrderSummaryHandler
    constructor(readDb: ReadDatabase)
    handle(query) -> OrderSummaryDTO
        return readDb.query("SELECT ... FROM order_summaries WHERE id = ?", query.orderId)

// Projector keeps read model in sync with write side
class OrderSummaryProjector
    on(OrderPlacedEvent e)  -> readDb.insert("order_summaries", { id: e.orderId, status: "placed", ... })
    on(OrderShippedEvent e) -> readDb.update("order_summaries", { status: "shipped" }, where: id = e.orderId)

// Optional bus wiring
commandBus.register(PlaceOrderCommand, PlaceOrderHandler)
queryBus.register(GetOrderSummaryQuery, GetOrderSummaryHandler)
```

## Example

```
// Food-delivery app — write enforces rules; read serves a flat customer dashboard

// Write: rich domain model with invariants
class Order
    static create(customerId, restaurant, items, address)
        if items.isEmpty()       -> throw EmptyOrderError
        if restaurant.isClosed() -> throw RestaurantClosedError
        return new Order(...)

class PlaceOrderHandler
    handle(cmd: PlaceOrderCommand)
        restaurant = restaurantRepo.findById(cmd.restaurantId)
        order = Order.create(cmd.customerId, restaurant, cmd.items, cmd.address)
        orderRepo.save(order)
        eventBus.publish(OrderPlacedEvent(order))

// Read: flat denormalised DTO — no domain rules
class CustomerDashboardHandler
    handle(query: CustomerDashboardQuery) -> List<DashboardRow>
        return readDb.select("order_summaries")
            .join("restaurant_views", on: "restaurant_id")
            .leftJoin("driver_views", on: "driver_id")
            .where(customerId: query.customerId)
            .toList()

// Projector synchronises read model from events
class OrderProjector
    on(OrderPlacedEvent e)    -> readDb.insert("order_summaries", { id: e.orderId, status: "placed" })
    on(DriverAssignedEvent e) -> readDb.update("order_summaries", { driverId: e.driverId }, where: id = e.orderId)
    on(OrderDeliveredEvent e) -> readDb.update("order_summaries", { status: "delivered" }, where: id = e.orderId)
```

## Trade-offs

+ Read and write models are optimised independently — queries stay fast and simple
+ Business rules are cleanly isolated on the write side
+ Read models can be rebuilt by replaying events; multiple projections serve different clients
- Significantly more code: two models, two handler sets, projectors, possibly two databases
- Read model lags behind write model (eventual consistency) — UI must tolerate stale data
- Debugging a request across command handlers, events, and projectors requires good tooling

## Related

- **Event Sourcing** — natural companion; the write-side event log is the source projectors consume
- **Repository** — used on the write side to load and persist aggregates
- **Mediator** — commonly implemented as the Command/Query Bus routing to handlers
