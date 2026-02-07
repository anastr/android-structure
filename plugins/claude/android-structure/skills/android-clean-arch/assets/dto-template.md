# Data Transfer Object (DTO)

- DTOs represent data as it comes from external sources (API, database).
- Use standard Kotlin naming conventions (camelCase) for properties.
- Use serialization annotations when API/DB uses different naming (e.g., snake_case).
- Never use DTOs in domain or presentation layers - always map to domain models.

**File:** `{basePackage}/data/model/user/UserDto.kt`

```kotlin
data class UserDto(
    val id: String,
    val email: String,
    @SerialName("first_name")
    val firstName: String,
    @SerialName("last_name")
    val lastName: String,
    @SerialName("avatar_url")
    val avatarUrl: String?,
    @SerialName("is_verified")
    val isVerified: Boolean,
    @SerialName("created_at")
    val createdAt: Long
)
```

## Guidelines

1. **Use camelCase**: Always use Kotlin naming conventions for properties
2. **Annotations for mapping**: Use serialization annotations when API/DB uses different naming
3. **Include all fields**: Even fields not used in domain should be captured
4. **No business logic**: DTOs are pure data containers
5. **Separate from domain**: Keep in `data/model/` package, never in `domain/`

## Serialization Annotations

| Library | Annotation |
|---------|------------|
| Kotlinx Serialization | `@SerialName("first_name")` |
| Gson | `@SerializedName("first_name")` |
| Moshi | `@Json(name = "first_name")` |
