# RedG runtime API

The most bare-bone approach to inserting data with RedG ist the runtime API. Using it directly is the most flexible way and does not
require any extra dependencies.

The runtime API can be used to either 

 * insert the data set via `PreparedStatements` into a database using a JDBc `Connection`.
 * generate SQL `INSERT` statements that can be used with some other tool (Oracle SqlDeveloper, JetBrains DataGrip, MySQL Workbench, etc.).
 
## Using PreparedStatements

To insert the of a RedG instance into a database, call the `insertDataIntoDatabase()` method and provide a JDBC connection.

!!! note
    You might need to use a custom 
    [PreparedStatement parameter setter](../customization/runtime/prepared_statement_parameter_setter.md) for special data types.

## Generating SQL statements

To generate SQL `INSERT` statements for your test data, call the `generateSQLStatements` method on your RedG object. It will return a
`List<String>` with each String being a complete SQL INSERT statement. The list is ordered so that no foreign key constraints are
violated, so preserve this order. When exporting the statements into an SQL file you have to append a semicolon to each statement.

!!! note
    You might need to use a custom 
    [SQL values formatter](../customization/runtime/sql_values_formatter.md) for special data types.

Example code for export:
````java
List<String> list = redG.generateSQLStatements();
String sqlScript = sql.stream().collect(Collectors.joining(";\n"));
// do whatever you want with sqlScript
````