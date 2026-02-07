# Compose Date Format

- Use it when you need to format dates in a composable function.
- Keep all your date/time formats in a single place.
- Place it in a module where it can be accessed by the whole project.

```kotlin
// 1. Define a stable interface with all required formats
@Stable
interface ComposeDateFormatter {
    fun fullDate(millis: Long): String
    fun shortDate(millis: Long): String
    fun monthName(millis: Long): String
    fun time12(millis: Long): String
}

// 2. Create immutable implementation with caching
@Immutable
private class ComposeDateFormatterImpl(
    private val locale: Locale
) : ComposeDateFormatter {

    private val formatterCache = mutableMapOf<String, DateTimeFormatter>()

    private val fullDateFormat
        get() = getCachedFormatter("dd MMMM yyyy")

    private val shortDateFormat
        get() = getCachedFormatter("dd/MM/yyyy")

    private val monthNameFormat
        get() = getCachedFormatter("MMMM")

    private val time12Format
        get() = getCachedFormatter("hh:mm a")

    override fun fullDate(millis: Long) = fullDateFormat.formatDate(millis)
    override fun shortDate(millis: Long) = shortDateFormat.formatDate(millis)
    override fun monthName(millis: Long) = monthNameFormat.formatDate(millis)
    override fun time12(millis: Long) = time12Format.formatDate(millis)

    private fun getCachedFormatter(pattern: String): DateTimeFormatter {
        return formatterCache.getOrPut(pattern) {
            DateTimeFormatter.ofPattern(pattern, locale)
                .withDecimalStyle(DecimalStyle.of(locale))
        }
    }
}

// 3. Provide a composable function to remember the formatter
@Composable
fun rememberDateFormatter(
    locale: Locale = LocalContext.current.resources.configuration.locales[0]
): ComposeDateFormatter {
    return remember(locale) { ComposeDateFormatterImpl(locale) }
}
```

**Usage:**
```kotlin
@Composable
fun UserProfile(state: UserState) {
    val dateFormatter = rememberDateFormatter()

    Text(text = "Member since: ${dateFormatter.fullDate(state.createdAtMillis)}")
    Text(text = "Last login: ${dateFormatter.time12(state.lastLoginMillis)}")
}
```

**Benefits:**
- `@Stable` interface prevents unnecessary recompositions
- Formatter is cached and remembered per locale
- All date formats are centralized in one place
- Locale changes are handled automatically
