# Hilt

- Use Hilt with KSP (avoid kapt) for better build performance.
- Bind the single repository implementation to both Read and Edit interfaces.
- Use cases inject only the interface they need.

**File:** `{basePackage}/di/UserModule.kt`

```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class UserModule {

    companion object {
        @Provides
        @Singleton
        fun provideUserRepositoryImpl(
            apiService: UserApiService,
            userDao: UserDao
        ): UserRepositoryImpl = UserRepositoryImpl(apiService, userDao)
    }

    @Binds
    abstract fun bindReadUserRepository(impl: UserRepositoryImpl): ReadUserRepository

    @Binds
    abstract fun bindEditUserRepository(impl: UserRepositoryImpl): EditUserRepository

    @Binds
    abstract fun bindGetUserUseCase(impl: GetUserUseCaseImpl): GetUserUseCase

    @Binds
    abstract fun bindSaveUserUseCase(impl: SaveUserUseCaseImpl): SaveUserUseCase
}
```
