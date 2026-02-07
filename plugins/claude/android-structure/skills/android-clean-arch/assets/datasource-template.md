# DataSource

- DataSources abstract the origin of data (network, database, cache, etc.).
- Each data source should focus on a single data origin.
- Repositories coordinate between multiple data sources.
- DataSources work with DTOs, not domain models.
- **Note:** Interfaces that already provide Kotlin-friendly abstractions (e.g., Retrofit API services, Room DAOs) can be injected directly into repositories. Create custom DataSource classes only when you need additional logic or abstraction over the raw data origin.

## Example: Wrapping an SDK

Use DataSource to wrap third-party SDKs that don't provide Kotlin-friendly interfaces:

**File:** `{basePackage}/data/datasource/AnalyticsDataSource.kt`

```kotlin
interface AnalyticsDataSource {
    suspend fun logEvent(name: String, params: Map<String, Any>)
    suspend fun setUserId(userId: String)
    suspend fun setUserProperty(name: String, value: String)
}
```

**File:** `{basePackage}/data/datasource/AnalyticsDataSourceImpl.kt`

```kotlin
internal class AnalyticsDataSourceImpl @Inject constructor(
    private val firebaseAnalytics: FirebaseAnalytics
) : AnalyticsDataSource {

    override suspend fun logEvent(name: String, params: Map<String, Any>) {
        val bundle = Bundle().apply {
            params.forEach { (key, value) ->
                when (value) {
                    is String -> putString(key, value)
                    is Int -> putInt(key, value)
                    is Long -> putLong(key, value)
                    is Double -> putDouble(key, value)
                    is Boolean -> putBoolean(key, value)
                }
            }
        }
        firebaseAnalytics.logEvent(name, bundle)
    }

    override suspend fun setUserId(userId: String) {
        firebaseAnalytics.setUserId(userId)
    }

    override suspend fun setUserProperty(name: String, value: String) {
        firebaseAnalytics.setUserProperty(name, value)
    }
}
```

This approach:
- Provides a clean Kotlin interface (Map instead of Bundle)
- Makes testing easier by allowing mock implementations
- Isolates SDK-specific code from the rest of the app

## Guidelines

1. **Internal visibility**: Always mark implementation classes as `internal` to prevent direct access from other modules
2. **Single data origin**: Each data source handles ONE data origin (remote, local, cache)
3. **Work with DTOs**: DataSources operate on DTOs, not domain models
4. **No business logic**: Keep data sources focused on data operations only
5. **Let exceptions propagate**: Don't catch exceptions here; let the repository handle error mapping
6. **Naming convention**: Use `Remote`, `Local`, or `Cache` suffix to indicate the data origin
7. **Interface segregation**: Define interfaces for each data source to enable testing and flexibility
8. **Skip DataSource wrapper when not needed**: Interfaces that already abstract data origins and return Kotlin-friendly code (e.g., Retrofit API services, Room DAOs) can be considered data sources themselves. Inject them directly into the repository instead of creating an extra DataSource wrapper layer
