description: Documentation for the PreparedStatement parameter setter of the RedG runtime
path: tree/main/redg-runtime/src/main/java/de/yamass/redg/runtime/transformer
source: PreparedStatementSetter.java
        DefaultPreparedStatementSetter.java

# PreparedStatement parameter setter

The PreparedStatement parameter setter is used by RedG set the column values for the `PreparedStatement`. It can be used
to convert a custom data type into a type the JDBC driver can understand.

!!! note
    If you are not using PreparedStatements with the `insertDataIntoDatabase()` method, you can ignore
    the PreparedStatement parameter setter and take a look at the 
    [SQL values formatter](sql_values_formatter.md)

## Default PreparedStatement parameter setter
The `DefaultPreparedStatementSetter` only calls `toString()` on the value if the JDBC type is one of 
`Types.CHAR, Types.VARCHAR, Types.LONGNVARCHAR`. Every other value is passed to the statement without modification.
 
This works for most standard java types and depending on your JDBC driver it might even work for the new Java time 
types. Consult the documentation of your JDBC driver for more information on supported types.

## Java API

If you need to implement your own setter, implement the `PreparedStatementParameterSetter` interface.

Inside of this method you should transform the object if needed. The `AttributeMetaInfo` provide you with metadata about
the object you are processing. After transforming the object, set it as the parameter on the passed `statement`.

!!! caution
    Only call `statement.set...()` with the index parameter specified in `parameterIndex`. RedG performs no further checks
    whether you actually set the right parameter (or any at all).

````java
@FunctionalInterface
public interface PreparedStatementParameterSetter {

    void setParameter(PreparedStatement statement, int parameterIndex, Object object, AttributeMetaInfo attributeMetaInfo, final Connection connection) throws SQLException;
}
````
