# Repository Interface

- Separate read and edit operations into different interfaces (Interface Segregation Principle).
- Inject only the interface you need: use `ReadUserRepository` for read-only operations.
- A single implementation class implements both interfaces.
- All methods should return `Response<T>` for consistent error handling.

**File:** `{basePackage}/domain/repository/UserRepository.kt`

```kotlin
interface ReadUserRepository {
    suspend fun getUser(id: String): Response<User>
    suspend fun getUsers(): Response<List<User>>
}

interface EditUserRepository : ReadUserRepository {
    suspend fun saveUser(user: User): Response<Unit>
    suspend fun deleteUser(id: String): Response<Unit>
}
```

## Guidelines

1. **Separate interfaces**: Split read and edit operations into separate interfaces
2. **Inject what you need**: Use `ReadUserRepository` for read-only use cases
3. **Edit extends Read**: `EditUserRepository` extends `ReadUserRepository` for convenience
4. **Skip EditRepository when not needed**: If the feature only fetches data (e.g., simple API data fetch), create only `ReadRepository`. Don't create `EditRepository` unless you have edit/update/delete operations
5. **Single implementation**: One class implements both interfaces (or just `ReadRepository` if no edit operations)
6. **Suspend functions**: All I/O operations should be suspending
7. **Response wrapper**: Always return `Response<T>`, never throw exceptions
8. **Domain types only**: Parameters and return types use domain models, not DTOs
