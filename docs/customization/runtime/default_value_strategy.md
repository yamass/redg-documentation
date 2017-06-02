description: Documentation for the default value strategy of the RedG runtime, how it can be extended or replaced
path: tree/master/redg-runtime/src/main/java/com/btc/redg/runtime/defaultvalues
source: DefaultValueStrategy.java
        DefaultDefaultValueStrategy.java
        pluggable/PluggableDefaultValueStrategy.java

# Default value strategy

The default value strategy is one of RedGs best features, and like most of RedG can be customized to fit your 
requirements. Out-of-the-box RedG provides two default value strategies: The `DefaultDefaultValueStrategy`, a 
minimalistic strategy, and the `PluggableDefaultValueStrategy`, a plug-in system that can be extended by 
either provided or custom plug-ins.

## Default default value strategy

RedG's default default value strategy supports the following data types and provides a fixed default value for them.

| Data type | Default value |
| :-------- | ------------- |
| `String` | `"-"` (Oracle does not support empty Strings and treats them as `NULL`) |
| `char` / `Character` | `' '` (whitespace character) |
| `boolean` / `boolean` | `false` |
| everything extending `Number` / primitive number types | `0` |
| everything extending `java.util.Date` | `0` (= `01.01.1970`) |
| Java 8 date types | `1970-01-01T00:00:00.000Z` |

If a column is a primary key or has a unique constraint, a value that is unique to that column 
(but may be the same as in other unique columns) is generated.

| Data type | Unique default value | Max number of unique values |
| :-------- | -------------------- | --------------------------- |
| `String` | `"A"`, `"B"`, ..., `"Z"`, `"AA"`, `"AB"` ... | 2^64 |
| `char` / `Character` | Unicode Character starting with `\u0001` up until `\uffff`  | (2^16)-1 = 65,535 |
| `boolean` / `boolean` | `false`, then `true` | 2 |
| everything extending `Number` / primitive number types | `0`, `1`, `2`, ... | 2^64 |
| everything extending `java.util.Date` | Counting up milliseconds since unix epoch, starting at `0` | until year 8099 |
| Java 8 date types |  Counting up milliseconds since unix epoch, starting at `0` | until year 8099 |

## Pluggable default value strategy

The `PluggableDefaultValueStrategy` has a list of `PluggableDefaultValueProvider`s. When a default value needs to be
generated, it checks whether a provider can provide a default value for the required data type / table / column. 
The values from all eligible providers are then collected and the first not-null value is returned as the default value.
If no eligible provider is found or all return `null`, `null` gets returned. Even if the column is nullable 
(`notNull == false`), a not-null value is preferred.
 
The following providers are bundles with RedG:

| Class name | Description |
| ---------- | ----------- |
| `ConstantStringProvider` | Provides a constant String which can be set by the user. |
| `ConstantValueProvider` | Provides a constant value for a certain data type. Returns the value if `value.getClass()` equals the exact required type. |
| `StaticNumberProvider` | Provides a static number. |
| `IncrementingNumberProvider` | Provides an incrementing number. The start value can be specified. The counter is incremented for each column separately. Ideal for index columns. |
| `StaticDateProvider` | Takes a `java.util.Date` as a parameter and returns it for **every** java date type. |
| `CurrentDateProvider` | Returns the current date / time for **every** java date type. |
| `ConditionalProvider` | Encapsulates another provider and will only return its value if the regular expressions for table & column name match. |
| `CustomConditionalProvider` | A conditional provider that is even more flexible that the `ConditionalProvider`. Is used by the builder system. |
| `DefaultDefaultValueProvider` | A provider encapsulating the `DefaultDefaultValueStrategy`. Use for fallback purposes. |

You can use the `PluggableDefaultValueStrategy.Builder()` class to easily build a highly customized system:

````java
PluggableDefaultValueStrategy strategy = new PluggableDefaultValueStrategy.Builder()
    .use(new IncrementingNumberProvider()).when(columnName(contains("_ID")))
    .use(new StaticNumberProvider(42))
    .useDefault() // shortcut for a DefaultDefaultValueProvider, perfect as fallback
    .build();
````

Take a look at the Javadoc for `com.btc.redg.runtime.defaultvalues.pluggable.buildermatchers.Matchers` and 
`com.btc.redg.runtime.defaultvalues.pluggable.buildermatchers.Conditions` for all available conditions to use in the `.when()` part.

### Custom provider implementation

Implementing a custom provider is easy. Simply implement the `PluggableDefaultValueProvider` interface and implement
both the `getDefaultValue` and `willProvide` method. The `willProvide` method should return `true` if `getDefaultValue`
can and should produce a default value for the current column and data type.

## Custom implementation

Custom default value strategies have to implement the `DefaultValueStrategy` interface.

If `notNull` is `true` and `null` is returned, RedG will fail. RedG does not perform further checks on the value
generated by this method. You have full control and responsibility.

````java
public interface DefaultValueStrategy {
    <T> T getDefaultValue(ColumnModel columnModel, Class<T> type);
}
````