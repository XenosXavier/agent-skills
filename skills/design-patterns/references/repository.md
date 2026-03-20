# Repository

**Intent:** Abstracts data access behind a collection-like interface so domain logic never depends on a specific persistence technology.

## Structure

```
interface UserRepository
    findById(id) -> User | null
    findByEmail(email) -> User | null
    findAll() -> List<User>
    save(user) -> void
    delete(id) -> void

class SqlUserRepository implements UserRepository
    constructor(db: DatabaseConnection)
    findById(id) -> db.query("SELECT * FROM users WHERE id = ?", id) |> mapRow
    save(user)   -> db.upsert("users", user)

class InMemoryUserRepository implements UserRepository
    store: Map<id, User> = {}
    findById(id) -> store[id]
    save(user)   -> store[user.id] = user

class UserService
    constructor(repo: UserRepository)   // interface only — no concrete type
    registerUser(email, password)
        if repo.findByEmail(email) != null -> throw DuplicateEmailError
        repo.save(User.create(email, password))
```

## Example

```
// Blog platform — same service code runs in prod and tests
interface PostRepository
    findById(id) -> Post | null
    findPublished() -> List<Post>
    save(post) -> void

class PostgresPostRepository implements PostRepository
    findPublished() -> db.query("SELECT * FROM posts WHERE published_at IS NOT NULL")
    save(post)      -> db.upsert("posts", post)

class InMemoryPostRepository implements PostRepository
    posts = []
    findPublished() -> posts.filter(p => p.publishedAt != null)
    save(post)      -> posts.push(post)

class PostService
    constructor(repo: PostRepository)
    publishPost(id)
        post = repo.findById(id)
        if post == null -> throw NotFoundError
        post.publishedAt = now()
        repo.save(post)

// Wiring
prodService = PostService(PostgresPostRepository(db))
testService = PostService(InMemoryPostRepository())
```

## Trade-offs

+ Domain logic is fully decoupled from the database technology
+ Swap storage (SQL → NoSQL) by writing one new class
+ Unit tests run without a real database
- Extra abstraction layer adds files and interfaces to maintain
- Easy to accumulate too many query methods ("God Repository")

## Related

- **Unit of Work** — groups multiple repository operations into one atomic transaction
- **Specification** — replaces per-query methods with a composable predicate passed to `findMatching(spec)`
- **Active Record** — the alternative where entities persist themselves; simpler but harder to test
