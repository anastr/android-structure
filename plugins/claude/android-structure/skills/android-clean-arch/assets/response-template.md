# Response Sealed Class

- This is a shared utility class used across the entire application.
- It wraps operation results to provide consistent error handling.
- Place this file once in your domain layer and reuse it for all features.

**File:** `{basePackage}/domain/model/Response.kt`

```kotlin
sealed class Response<out T> {
    data class Success<T>(val data: T) : Response<T>()
    data class Error(
        val exception: Throwable,
        val message: String? = exception.message
    ) : Response<Nothing>()
}

inline fun <T, R> Response<T>.fold(
    onSuccess: (T) -> R,
    onError: (Throwable, String?) -> R
): R = when (this) {
    is Response.Success -> onSuccess(data)
    is Response.Error -> onError(exception, message)
}

inline fun <T, R> Response<T>.map(transform: (T) -> R): Response<R> = when (this) {
    is Response.Success -> Response.Success(transform(data))
    is Response.Error -> this
}
```

## Usage

```kotlin
response.fold(
    onSuccess = { user -> showUser(user) },
    onError = { error, message -> showError(message) }
)

val namesResponse = usersResponse.map { users -> users.map { it.name } }
```
