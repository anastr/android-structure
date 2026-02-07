---
name: android-modularization
description: Multi-module Android project structure and best practices for scalable applications.
license: Apache-2.0
compatibility: Native Android projects using Kotlin and Gradle.
metadata:
    author: Anas-Altair
    version: "1.0.0"
---

# Android Modularization Skill

> Best practices for structuring multi-module Android projects.

## Project Structure

```
project/
├── app/                    # Main application module
├── feature/
│   ├── auth/               # Authentication feature
│   ├── profile/            # User profile feature
│   └── settings/           # Settings feature
├── core/
│   ├── network/            # Networking (Retrofit, OkHttp)
│   ├── database/           # Local database (Room)
│   └── analytics/          # Analytics tracking
└── common/
    ├── ui/                 # Shared UI components
    └── utils/              # Shared utilities
```

---

## Module Types

### 1. :app

The main application module that assembles all features.

**Contains:**
- `Application` class
- `MainActivity` and navigation host
- App-level dependency injection setup

**Dependencies:**
```kotlin
dependencies {
    implementation(project(":feature:auth"))
    implementation(project(":feature:profile"))
    implementation(project(":feature:settings"))
    implementation(project(":common:ui"))
}
```

---

### 2. :feature:*

Feature modules contain UI and business logic for a specific feature.

**Contains:**
- Screens and ViewModels
- Feature-specific use cases
- Navigation within the feature

**Rules:**
- Feature modules should NOT depend on other feature modules
- Depend only on `:core:*` and `:common:*` modules

**Dependencies:**
```kotlin
dependencies {
    implementation(project(":core:network"))
    implementation(project(":core:database"))
    implementation(project(":common:ui"))
    implementation(project(":common:utils"))
}
```

---

### 3. :core:*

Core modules provide shared infrastructure and services.

**Examples:**

| Module | Purpose |
|--------|---------|
| `:core:network` | Retrofit, OkHttp, API services |
| `:core:database` | Room database, DAOs |
| `:core:analytics` | Analytics tracking |
| `:core:auth` | Authentication logic |

**Rules:**
- No Android UI dependencies (no Compose, Views, etc.)
- Should be pure Kotlin when possible
- Can depend on other `:core:*` modules if needed

---

### 4. :common:*

Common modules contain shared utilities and resources.

**Examples:**

| Module | Purpose |
|--------|---------|
| `:common:ui` | Shared Compose components, themes |
| `:common:utils` | Extensions, helpers, constants |
| `:common:testing` | Shared test utilities |

**Rules:**
- `:common:ui` can have Android UI dependencies
- `:common:utils` should be pure Kotlin

---

## Best Practices

### 1. No Feature-to-Feature Dependencies

Feature modules must NOT depend on each other. Use navigation or shared interfaces instead.

```kotlin
// BAD - Direct dependency
// :feature:profile depends on :feature:auth
implementation(project(":feature:auth"))

// GOOD - Use navigation or shared core module
implementation(project(":core:auth"))
```

### 2. Use `api` vs `implementation` Correctly

```kotlin
dependencies {
    // Use 'implementation' by default - keeps dependency private
    implementation(project(":core:network"))

    // Use 'api' only when dependents need transitive access
    api(project(":common:utils"))
}
```

### 3. Keep Dependencies Minimal

Each module should only depend on what it directly needs.

### 4. Internal Package Structure

Use the `android-clean-arch` skill for package structure within each module.

### 5. Resource Naming Prefix

Drawables and other resources with the same name in different modules can override each other. Add a short module prefix to resource names to avoid conflicts.

```
# BAD - May conflict with other modules
ic_logo.xml
bg_header.xml

# GOOD - Prefixed with module name
auth_ic_logo.xml
auth_bg_header.xml

profile_ic_avatar.xml
profile_bg_header.xml
```

---

## Dependency Graph

```
:app
  └── :feature:*
        ├── :core:*
        └── :common:*
              └── :core:* (optional)
```

**Key Rule:** Dependencies flow downward only. Lower modules never depend on higher ones.

---

## License

This skill is licensed under the Apache License 2.0.
