# Data Mapper

- Data mappers convert DTOs to domain models and vice versa.
- They bridge the gap between external data structures and business entities.
- Implemented as extension functions for clean, readable code.
- Handle any data transformation or normalization needed.

**File:** `{basePackage}/data/mapper/UserMapper.kt`

```kotlin
fun UserDto.toDomain(): User = User(
    id = this.id,
    email = this.email,
    fullName = "${this.firstName} ${this.lastName}".trim(),
    avatarUrl = this.avatarUrl,
    isVerified = this.isVerified
)

fun List<UserDto>.toDomain(): List<User> = map { it.toDomain() }
```

## Guidelines

1. **Extension functions**: Implement as extensions for fluent syntax
2. **Bidirectional**: Provide both `toDomain()` and `toDto()` when needed
3. **List helpers**: Include list mapping extensions for convenience
4. **Handle nulls**: Apply sensible defaults or propagate nullability
5. **Data transformation**: This is the place for any data manipulation between layers
6. **Parameters for dependencies** (optional): Mapper functions can receive parameters for simple operations like date parsing

```kotlin
fun UserDto.toDomain(dateParser: DateParser): User = User(
    id = this.id,
    email = this.email,
    fullName = "${this.firstName} ${this.lastName}".trim(),
    createdAtMillis = dateParser.toMillis(this.createdAt) // "2024-01-15" -> 1705276800000
)
```
