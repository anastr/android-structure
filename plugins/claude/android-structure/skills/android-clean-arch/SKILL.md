---
name: android-clean-arch
description: Generates Android Clean Architecture structure and boilerplate code using Kotlin. Creates Domain and Data layers with proper separation of concerns, testable interfaces, and dependency injection setup (Hilt or Koin). Use when scaffolding new features in Android applications following clean architecture principles.
license: Apache-2.0
compatibility: Native Android projects using Kotlin.
metadata:
    author: Anas-Altair
    version: "1.0.0"
---

# Android Clean Architecture Skill

> An open-format skill for AI agents to generate Android Clean Architecture structure using Kotlin.

## Description

This skill generates the structure and boilerplate code for Android applications following Clean Architecture principles. It creates **Domain** and **Data** layers with proper separation of concerns, testable interfaces, and dependency injection setup.

**Key Principles:**
- Structure only - does not modify business logic
- One domain/data layer per module (not per feature)
- Interface-based design for testability
- Immutable data models
- Clear layer boundaries
- Implementation classes are `internal` to enforce module encapsulation

---

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `basePackage` | Yes | - | Base package path (e.g., `com.example.app`) |
| `diLibrary` | No | `hilt` | Dependency injection framework: `hilt` or `koin` |

---

## Instructions for AI Agents

When this skill is invoked, follow these steps:

### Step 1: Validate Parameters
- Ensure `basePackage` is a valid Java/Kotlin package path
- Default `diLibrary` to `hilt` if not specified

### Step 2: Create Package Structure
Create the following directory structure under the source directory:

```
{basePackage}/
├── domain/
│   ├── model/
│   │   └── {featureName}/
│   ├── repository/
│   ├── usecase/
│   └── mapper/
├── data/
│   ├── model/
│   │   └── {featureName}/
│   ├── datasource/
│   ├── repository/
│   ├── usecase/
│   └── mapper/
├── ui/
│   ├── nav/
│   ├── screen/
│   │   └── {screen_name}/
│   ├── reusable/
│   └── fake/
└── di/
```

### Step 3: Generate Core Files
Generate the following files based on the templates below.

---

## Templates

All templates are stored in the `assets/` directory. Each template includes:
- Explanation of the component's purpose
- Real examples using "User" as the feature name
- Guidelines and best practices

### Domain Layer

| Component | Template | Description |
|-----------|----------|-------------|
| Response | [response-template.md](assets/response-template.md) | Shared sealed class for operation results |
| Domain Model | [domain-model-template.md](assets/domain-model-template.md) | Immutable business entities |
| Repository Interface | [repository-interface-template.md](assets/repository-interface-template.md) | Data operation contracts |
| UseCase Interface | [usecase-interface-template.md](assets/usecase-interface-template.md) | Business action contracts |
| Domain Mapper | [domain-mapper-template.md](assets/domain-mapper-template.md) | Domain-to-domain transformations (optional) |

### Data Layer

| Component | Template | Description |
|-----------|----------|-------------|
| DTO | [dto-template.md](assets/dto-template.md) | Data Transfer Objects for API/DB |
| DataSource | [datasource-template.md](assets/datasource-template.md) | Abstracts data origins (API, DB, cache) |
| Data Mapper | [data-mapper-template.md](assets/data-mapper-template.md) | DTO to domain conversions |
| Repository Impl | [repository-impl-template.md](assets/repository-impl-template.md) | Concrete data operations |
| UseCase Impl | [usecase-impl-template.md](assets/usecase-impl-template.md) | Business logic implementation |

### UI Layer

| Component | Template | Description |
|-----------|----------|-------------|
| UI | [ui-template.md](assets/ui-template.md) | Screens, reusable components, items, and fake data |

### Dependency Injection

| Framework | Template | Description |
|-----------|----------|-------------|
| Hilt | [hilt-template.md](assets/hilt-template.md) | Hilt module with @Binds |
| Koin | [koin-template.md](assets/koin-template.md) | Koin module DSL |

**Note:** Use Hilt with KSP (avoid kapt) for better build performance.

---

## UseCase Best Practices

1. **Single Responsibility**: Each use case should do exactly one thing
   - `GetUserUseCase` - retrieves a user
   - `SaveUserUseCase` - saves a user
   - `ValidateUserEmailUseCase` - validates email format

2. **Naming Convention**: `Verb` + `Noun` + `UseCase`
   - `GetUserUseCase`
   - `SaveOrderUseCase`
   - `DeleteProductUseCase`
   - `ValidatePaymentUseCase`

3. **Operator Invoke**: Use `operator fun invoke()` for clean syntax
   ```kotlin
   // Instead of: useCase.execute(id)
   // You can write: useCase(id)
   ```

4. **Multiple Repositories**: Use cases can coordinate multiple repositories
   ```kotlin
   class CreateOrderUseCaseImpl @Inject constructor(
       private val orderRepository: OrderRepository,
       private val inventoryRepository: InventoryRepository,
       private val paymentRepository: PaymentRepository
   ) : CreateOrderUseCase {
       // Implementation
    }
   ```

5. **No Android Dependencies**: Domain layer should be pure Kotlin
   - No `Context`, `Activity`, `View`, etc.
   - Enables unit testing without Android framework

6. **Consistent Return Type**: Always return `Response<T>`
   - Enables uniform error handling
   - Makes the API predictable

7. **Skip UseCase for Simple Calls**: Don't create a use case for a single repository function call
   - Use the repository directly instead
   - Use cases are meant for business logic, not for wrapping repository calls
   - Create a use case only when there's actual logic to encapsulate (validation, coordination, transformation)

---

## Example Invocation

**Request:**
```
Create clean architecture structure for feature "user"
with base package "com.myapp.features" using Hilt
```

**Parameters:**
- `basePackage`: `com.myapp.features`
- `diLibrary`: `hilt`

**Generated Files:**
```
com/myapp/
├── domain/
│   ├── model/
│   │   ├── Response.kt
│   │   └── user/
│   │       └── User.kt
│   ├── repository/
│   │   └── UserRepository.kt
│   ├── usecase/
│   │   └── GetUserUseCase.kt
│   └── mapper/
│       └── UserMapper.kt
├── data/
│   ├── model/
│   │   └── user/
│   │       └── UserDto.kt
│   ├── repository/
│   │   └── UserRepositoryImpl.kt
│   ├── usecase/
│   │   └── GetUserUseCaseImpl.kt
│   └── mapper/
│       └── UserMapper.kt
├── ui/
│   ├── screen/
│   │   └── profile/
│   │       ├── item/
│   │       │   └── UserCard.kt
│   │       ├── ProfileScreen.kt
│   │       └── ProfileViewModel.kt
│   └── fake/
│       └── FakeUser.kt
└── di/
    └── UserModule.kt
```

---

## License

This skill is licensed under the Apache License 2.0.
