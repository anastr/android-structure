# UseCase Implementation

- Use case implementations contain the actual business logic.
- Inject only the repository interface you need: `ReadUserRepository` or `EditUserRepository`.
- Can inject multiple repositories when operations span entities.
- Keep them focused on a single responsibility.

**File:** `{basePackage}/data/usecase/GetUserUseCaseImpl.kt`

```kotlin
internal class GetUserUseCaseImpl @Inject constructor(
    private val userRepository: ReadUserRepository
) : GetUserUseCase {

    override suspend operator fun invoke(id: String): Response<User> {
        return userRepository.getUser(id)
    }
}
```

**File:** `{basePackage}/data/usecase/SaveUserUseCaseImpl.kt`

```kotlin
internal class SaveUserUseCaseImpl @Inject constructor(
    private val userRepository: EditUserRepository
) : SaveUserUseCase {

    override suspend operator fun invoke(user: User): Response<Unit> {
        return userRepository.saveUser(user)
    }
}
```

## Guidelines

1. **Internal visibility**: Always mark implementation classes as `internal` to prevent direct access from other modules
2. **Inject the right interface**: Use `ReadRepository` for read-only, `EditRepository` for modifications
3. **Single responsibility**: Each use case does ONE thing
4. **Business logic home**: Complex rules and validations live here
5. **Repository coordination**: Combine data from multiple sources when needed
6. **No framework dependencies**: Keep as pure Kotlin
7. **Testability**: Easy to unit test with mocked repositories
