# Specification

**Intent:** Encapsulates a business rule as a reusable, composable predicate object so the same rule can be used for validation, in-memory filtering, and database queries.

## Structure

```
interface Specification<T>
    isSatisfiedBy(candidate: T) -> bool
    and(other: Specification<T>) -> Specification<T>
    or(other: Specification<T>)  -> Specification<T>
    not() -> Specification<T>

// Base class wires up the combinators
abstract class CompositeSpecification<T> implements Specification<T>
    and(other) -> AndSpecification(this, other)
    or(other)  -> OrSpecification(this, other)
    not()      -> NotSpecification(this)

class AndSpecification<T> extends CompositeSpecification<T>
    constructor(left, right)
    isSatisfiedBy(c) -> left.isSatisfiedBy(c) AND right.isSatisfiedBy(c)

class OrSpecification<T> extends CompositeSpecification<T>
    constructor(left, right)
    isSatisfiedBy(c) -> left.isSatisfiedBy(c) OR right.isSatisfiedBy(c)

class NotSpecification<T> extends CompositeSpecification<T>
    constructor(inner)
    isSatisfiedBy(c) -> NOT inner.isSatisfiedBy(c)

// Concrete rules
class ActiveUserSpec  extends CompositeSpecification<User>
    isSatisfiedBy(user) -> user.status == "active"

class PremiumUserSpec extends CompositeSpecification<User>
    isSatisfiedBy(user) -> user.subscriptionTier == "premium"
```

## Example

```
// E-commerce — same rules used for checkout validation, search filtering, and campaigns

class InStockSpec        extends CompositeSpecification<Product>
    isSatisfiedBy(p) -> p.stockQuantity > 0

class ActiveProductSpec  extends CompositeSpecification<Product>
    isSatisfiedBy(p) -> p.isActive

class DiscountEligibleSpec extends CompositeSpecification<Product>
    isSatisfiedBy(p) -> p.discountEligible

class InCategorySpec     extends CompositeSpecification<Product>
    constructor(category)
    isSatisfiedBy(p) -> p.category == category

// Compose complex rules from simple pieces
availableForSale   = InStockSpec().and(ActiveProductSpec())
summerSaleEligible = availableForSale
    .and(DiscountEligibleSpec())
    .and(InCategorySpec("apparel").or(InCategorySpec("footwear")))

// Use 1: domain validation in a service
class CartService
    addToCart(product, cart)
        if NOT availableForSale.isSatisfiedBy(product) -> throw ProductUnavailableError

// Use 2: filter an in-memory collection
featured = catalog.filter(p => availableForSale.isSatisfiedBy(p))

// Use 3: repository query
class ProductRepository
    findMatching(spec: Specification<Product>) -> List<Product>
        return allProducts.filter(p => spec.isSatisfiedBy(p))
        // or translate spec to a SQL/ORM predicate for DB-level filtering

saleCandidates = productRepo.findMatching(summerSaleEligible)
```

## Trade-offs

+ Single named home for each business rule — no duplication across validation, filtering, and querying
+ Complex conditions built from small, independently-testable pieces
+ New rule combinations require no new methods — just compose existing specs
- Boilerplate overhead for one-off conditions that are clearer as a plain boolean expression
- Translating specifications to efficient database queries requires a separate query-translation layer

## Related

- **Repository** — primary consumer; `findMatching(spec)` avoids one method per query variant
- **Strategy** — Specification is a specialised Strategy scoped to predicate evaluation
- **Composite (GoF)** — the AND/OR/NOT combinators follow the Composite tree structure
