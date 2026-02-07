# Domain Mapper

- Domain mappers transform between different domain models.
- Use when you need to convert one domain model to another.
- This is optional - only create when needed for transformations within the domain layer.
- Common use case: creating summary or display-specific versions of models.

**File:** `{basePackage}/domain/mapper/UserMapper.kt`

```kotlin
fun User.toSummary(): UserSummary = UserSummary(
    id = this.id,
    displayName = this.fullName,
    avatarUrl = this.avatarUrl
)
```

## When to Use

- Converting full models to summary/lightweight versions
- Creating view-specific representations
- Aggregating data from multiple domain models

## When NOT to Use

- Converting DTOs to domain models (use Data Mapper instead)
- Simple property access (just use the model directly)

## Guidelines

1. **Parameters for dependencies** (optional): Mapper functions can receive parameters for simple operations like date parsing

```kotlin
fun User.toDto(dateParser: DateParser): UserDto = UserDto(
    id = this.id,
    fullName = this.fullName,
    createdAt = dateParser.toMillis(this.createdAtString) // "2024-01-15" -> 1705276800000
)
```
