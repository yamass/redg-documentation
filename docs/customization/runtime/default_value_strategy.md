description: Documentation for the default value strategy of the RedG runtime, how it can be extended or replaced
path: tree/main/redg-runtime/src/main/java/de/yamass/redg/runtime/defaultvalues
source: DefaultValueStrategy.java
        DefaultDefaultValueStrategy.java
        pluggable/PluggableDefaultValueStrategy.java

# DefaultValueStrategy

The default value strategy is one of RedGs most important features, and like most of RedG can be customized to fit your 
requirements. 

The default implementation is `DefaultDefaultValueStrategy`. It delivers null for any nullable columns and a fixed non-null 
value for `NOT NULL` columns. However, most of the time, the `DefaultDefaultValueStrategy` will _not_ completely meet your needs. 

You could easily write your own `DefaultValueStrategy` from scratch, but we recommend using the `DefaultValueStrategyBuilder`.
It provides a convenient and clear API for creating custom `DefaultValueStrategy`s.

## DefaultDefaultValueStrategy

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

## DefaultValueStrategyBuilder

`DefaultValueStrategyBuilder` is the recommended way of creating `DefaultValueStrategy`s. It provides a convenient and clear API
for creating `DefaultValueStrategy`s. 

````java
DefaultValueStrategyBuilder builder = new DefaultValueStrategyBuilder();

builder.whenColumnNameMatches("ID")
        .thenUseProvider(new IncrementingNumberProvider());
builder.whenTableNameMatches(".*CARD").andColumnNameMatches("TYPE")
        .thenUse("MASTERCARD");
builder.when(columnModel -> columnModel.getDbFullTableName().equals("CCM.CREDITCARD.UUID"))
        .thenCompute((columnModel, expectedType) -> UUID.randomUUID().toString());

builder.build();
````

By default `DefaultValueStrategyBuilder` will add a `DefaultDefaultValueStrategy` as fallback value provider. However, you can change this using 
`DefaultValueStrategyBuilder.setFallbackStrategy(DefaultValueStrategy)`.

## PluggableDefaultValueStrategy

This is the `DefaultValueStrategy` implementation that the `DefaultValueStrategyBuilder` uses internally.

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