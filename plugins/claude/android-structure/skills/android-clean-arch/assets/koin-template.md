# Koin

- Bind the single repository implementation to both Read and Edit interfaces.
- Use cases inject only the interface they need.

**File:** `{basePackage}/di/UserModule.kt`

```kotlin
val userModule = module {
    single { UserRepositoryImpl(get(), get()) }
    single<ReadUserRepository> { get<UserRepositoryImpl>() }
    single<EditUserRepository> { get<UserRepositoryImpl>() }

    factory<GetUserUseCase> { GetUserUseCaseImpl(get()) }
    factory<SaveUserUseCase> { SaveUserUseCaseImpl(get()) }
}
```
