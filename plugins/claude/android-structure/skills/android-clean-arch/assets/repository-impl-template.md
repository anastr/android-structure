# Repository Implementation

- A single implementation class implements both `ReadUserRepository` and `EditUserRepository`.
- Repository implementations live in the data layer.
- Handle API calls, database operations, and caching logic here.
- Always catch exceptions and return `Response.Error` instead of throwing.

**File:** `{basePackage}/data/repository/UserRepositoryImpl.kt`

```kotlin
internal class UserRepositoryImpl @Inject constructor(
    private val apiService: UserApiService,
    private val userDao: UserDao
) : EditUserRepository {

    override suspend fun getUser(id: String): Response<User> {
        return try {
            val dto = apiService.getUser(id)
            userDao.insert(dto)
            Response.Success(dto.toDomain())
        } catch (e: Exception) {
            val cached = userDao.getUser(id)
            if (cached != null) {
                Response.Success(cached.toDomain())
            } else {
                Response.Error(e)
            }
        }
    }

    override suspend fun getUsers(): Response<List<User>> {
        return try {
            val dtos = apiService.getUsers()
            userDao.insertAll(dtos)
            Response.Success(dtos.toDomain())
        } catch (e: Exception) {
            Response.Error(e)
        }
    }

    override suspend fun saveUser(user: User): Response<Unit> {
        return try {
            apiService.saveUser(user.toDto())
            userDao.insert(user.toDto())
            Response.Success(Unit)
        } catch (e: Exception) {
            Response.Error(e)
        }
    }

    override suspend fun deleteUser(id: String): Response<Unit> {
        return try {
            apiService.deleteUser(id)
            userDao.delete(id)
            Response.Success(Unit)
        } catch (e: Exception) {
            Response.Error(e)
        }
    }
}
```

## Guidelines

1. **Internal visibility**: Always mark implementation classes as `internal` to prevent direct access from other modules
2. **Single implementation**: One class implements all repository interfaces for the feature
3. **Implements what's needed**: Implement only `ReadRepository` for read-only features, or `EditRepository` (which extends Read) when edit operations are needed
4. **Dependency injection**: Use constructor injection for data sources
5. **Never throw**: Catch all exceptions and return `Response.Error`
6. **Map at boundaries**: Convert DTOs to domain models before returning
7. **Caching strategy**: Implement appropriate caching (cache-first, network-first, etc.)
