# UI Layer

- The UI layer contains all Compose components, screens, and preview utilities.
- Organize screens by feature in the `screen/{screen_name}/` package.
- Extract reusable components that need to be used across screens to the `reusable/` package.
- Keep fake data for previews in the `fake/` package.

## Package Structure

| Package | Purpose                              |
|---------|--------------------------------------|
| `nav/` | Navigation setup and routes          |
| `screen/{screen_name}/` | Screen composables and its viewmodel |
| `screen/{screen_name}/item/` | Screen-specific UI items             |
| `reusable/` | Shared UI components across screens  |
| `fake/` | Fake data for Compose previews       |

---

## Reusable Component

Shared components that are used across multiple screens belong in the `reusable/` package.

**File:** `{basePackage}/ui/reusable/UserAvatar.kt`

```kotlin
@Composable
fun UserAvatar(
    imageUrl: String,
    modifier: Modifier = Modifier
) {
    // Implement your avatar composable here
}

@Preview
@Composable
private fun UserAvatarPreview() {
    UserAvatar(imageUrl = fakeUser().avatarUrl)
}
```

---

## Screen Item

UI items specific to a screen should be placed in the `screen_name/item/` package to keep the screen file clean.

**File:** `{basePackage}/ui/screen/profile/item/UserInfoCard.kt`

```kotlin
@Composable
fun UserInfoCard(
    user: User,
    modifier: Modifier = Modifier
) {
    // Implement your card composable here
}

@Preview
@Composable
private fun UserInfoCardPreview() {
    UserInfoCard(user = fakeUser())
}
```

---

## Screen

The main screen composable that combines items and reusable components.

**File:** `{basePackage}/ui/screen/profile/ProfileScreen.kt`

```kotlin
@Composable
fun ProfileScreen(state: ProfileState) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        if (state.isLoading) {
            CircularProgressIndicator()
        } else {
            state.user?.let { user ->
                UserInfoCard(user = user)
            }
        }
    }
}

@Preview
@Composable
private fun ProfileScreenPreview() {
    ProfileScreen(state = fakeProfileState())
}
```

---

## Fake Data

Fake data for previews. **Must be functions, not objects** to avoid memory allocation on app startup.

**File:** `{basePackage}/ui/fake/FakeUser.kt`

```kotlin
// Use function, NOT object - avoids loading to memory on every app launch
fun fakeUser(): User = User(
    id = "1",
    name = "Anas Altair",
    email = "anas.altair@example.com",
    avatarUrl = "https://example.com/avatar.jpg"
)

fun fakeProfileState(): ProfileState = ProfileState(
    user = fakeUser(),
    isLoading = false,
    error = null
)
```

---

## Guidelines

1. **Reusable components**: Add shared UI components to `reusable/` package for use across multiple screens
2. **Screen items**: Separate screen-specific UI items to `screen_name/item/` package to keep screen files clean and focused
3. **Previews required**: Add `@Preview` for all components, items, and screens
4. **Fake data as functions**: Create fake states as functions (not objects) to avoid memory allocation on app startup - they are only instantiated when previews are rendered
