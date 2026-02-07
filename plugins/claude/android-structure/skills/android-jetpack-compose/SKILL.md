---
name: android-jetpack-compose
description: Generate Android Jetpack Compose UI structure and tips to reduce recomposition and improve performance.
license: Apache-2.0
compatibility: Native Android projects using Kotlin and Jetpack Compose.
metadata:
    author: Anas-Altair
    version: "1.0.0"
---

# Android Jetpack Compose Skill

> Best practices and rules to reduce recomposition and improve Compose performance.

## Best Practices

### 1. Immutable Models

- Use immutable models only in composables to reduce unnecessary recomposition.
- Data classes with immutable properties are immutable by default.
- Mark sealed classes with `@Immutable` or `@Stable` annotations and make sure they are not mutable.

---

### 2. Immutable Collections

- Use `kotlinx-collections-immutable` library instead of standard Java collections. Java collections (like `List`, `Set`, `Map`) are considered mutable by the Compose compiler, causing unnecessary recomposition.

```kotlin
// BAD - List is considered mutable, causes recomposition
@Composable
fun UserList(users: List<User>) {  }

// GOOD - ImmutableList is stable, skips recomposition when unchanged
@Composable
fun UserList(users: ImmutableList<User>) {  }
```

**Dependency:**
```kotlin
implementation("org.jetbrains.kotlinx:kotlinx-collections-immutable:$kotlinx_collections_version")
```

**Usage:**
```kotlin
val users: ImmutableList<User> = persistentListOf(user1, user2)
// or convert from List
val users: ImmutableList<User> = userList.toImmutableList()
```

---

### 3. Reusable Components Package

Keep basic and reusable components in a separate package to be used across all screens.

```
ui/
├── reusable/          # Shared components
│   ├── UserAvatar.kt
│   ├── PrimaryButton.kt
│   └── LoadingIndicator.kt
└── screen/
    └── profile/
        ├── item/      # Screen-specific UI items
        ├── ProfileScreen.kt
        └── ProfileViewModel.kt
```

See the `android-clean-arch` skill for detailed UI package structure.

---

### 4. Material Theme Colors

Don't use hardcoded colors in composables. Add colors to Material Theme and reference them via `MaterialTheme.colorScheme`.

```kotlin
// BAD - Hardcoded color
Text(
    text = "Hello",
    color = Color(0xFF1976D2)
)

// GOOD - Use Material Theme
Text(
    text = "Hello",
    color = MaterialTheme.colorScheme.primary
)
```

**Custom colors** should be added to your theme:

```kotlin
private val LightColorScheme = lightColorScheme(
    primary = Color(0xFF1976D2),
    secondary = Color(0xFF03DAC6),
    // ...
)

@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colorScheme = LightColorScheme,
        content = content
    )
}
```

**Extended custom colors** - When you need colors beyond Material's colorScheme:

```kotlin
// 1. Create a data class for extended colors
@Immutable
data class ExtendedColors(
    val success: Color,
    val warning: Color,
    val info: Color
)

// 2. Create CompositionLocal
val LocalExtendedColors = staticCompositionLocalOf {
    ExtendedColors(
        success = Color.Unspecified,
        warning = Color.Unspecified,
        info = Color.Unspecified
    )
}

// 3. Define your extended colors
private val LightExtendedColors = ExtendedColors(
    success = Color(0xFF4CAF50),
    warning = Color(0xFFFF9800),
    info = Color(0xFF2196F3)
)

// 4. Wrap theme with CompositionLocalProvider
@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    CompositionLocalProvider(LocalExtendedColors provides LightExtendedColors) {
        MaterialTheme(
            colorScheme = LightColorScheme,
            content = content
        )
    }
}

// 5. Create extension for easy access
val MaterialTheme.extendedColors: ExtendedColors
    @Composable
    @ReadOnlyComposable
    get() = LocalExtendedColors.current
```

**Usage:**
```kotlin
Text(
    text = "Success!",
    color = MaterialTheme.extendedColors.success
)
```

---

### 5. Date Formatting in UI

Date formatting should be handled in the UI layer, not in domain or data layers. Pass raw date values (timestamps, `LocalDate`, etc.) to composables and format them at display time.

Create a `@Stable` interface with all date formats required by the project, and use `remember` to avoid recompositions:

Check [date format template](assets/date-format-template.md) to see the template when you need to format dates in UI.

---

## License

This skill is licensed under the Apache License 2.0.
