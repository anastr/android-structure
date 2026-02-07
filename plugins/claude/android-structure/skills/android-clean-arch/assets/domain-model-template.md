# Domain Model

- Domain models represent the core business entities of your application.
- They are immutable data classes used across domain and presentation layers.
- Keep them free of framework dependencies (no Android imports).
- Add only properties that are meaningful to your business logic.

**File:** `{basePackage}/domain/model/user/User.kt`

```kotlin
data class User(
    val id: String,
    val email: String,
    val fullName: String,
    val avatarUrl: String?,
    val isVerified: Boolean
)
```

## Guidelines

1. **Immutability**: Always use `val` properties, never `var`
2. **Nullability**: Use nullable types (`?`) only when the absence of value is meaningful
3. **No default values**: Avoid defaults unless they represent true business defaults
4. **No framework dependencies**: This class should compile without Android SDK
