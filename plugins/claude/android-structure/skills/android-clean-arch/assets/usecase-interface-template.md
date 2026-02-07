# UseCase Interface

- Use cases encapsulate a single business action.
- They define the contract for business operations.
- Use `operator fun invoke()` for clean call-site syntax.
- Each use case should do exactly ONE thing.

**File:** `{basePackage}/domain/usecase/GetUserUseCase.kt`

```kotlin
interface GetUserUseCase {
    suspend operator fun invoke(id: String): Response<User>
}
```

## Naming Convention

Follow the pattern: **Verb + Noun + UseCase**

| Action | Example |
|--------|---------|
| Retrieve single | `GetUserUseCase` |
| Retrieve list | `GetUsersUseCase` |
| Create | `CreateUserUseCase` |
| Update | `UpdateUserUseCase` |
| Delete | `DeleteUserUseCase` |
| Validate | `ValidateUserEmailUseCase` |

## Guidelines

1. **Single responsibility**: One use case = one action
2. **Operator invoke**: Use `operator fun invoke()` for clean syntax
3. **Descriptive names**: The name should describe the action clearly
4. **No Android dependencies**: Keep use cases as pure Kotlin
5. **Skip use case for simple calls**: Don't create a use case for a single repository function call. Use the repository directly instead. Use cases are meant for business logic, not for wrapping repository calls
