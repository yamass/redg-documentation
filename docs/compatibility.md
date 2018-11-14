# Compatibility

RedG is able to work with the most common database systems. All you need is a JDBC driver.
The Code Generator internally uses [SchemaCrawler](http://sualeh.github.io/SchemaCrawler) which supports a 
[wide range of database systems](http://sualeh.github.io/SchemaCrawler/database-support.html). You'll probably need an extra 
SchemaCrawler plugin, so consult the table below or see [Maven Central](https://mvnrepository.com/artifact/us.fatehi) for a full list.

| DBMS | Necessary extra dependency | Example project |
| ---- | -------------------- | --------------- |
| H2 | Dependency included in RedG | redg-examples/redg-example-h2 | 
| Oracle | `us.fatehi:schemacrawler-oracle:15.01.06` | redg-examples/redg-example-oracle |
| IBM DB2 | `us.fatehi:schemacrawler-db2:15.01.06` | No example, support untested |
| MS SQL Server¹ | `us.fatehi:schemacrawler-sqlserver:15.01.06` | No example, support untested |
| MySQL² | `us.fatehi:schemacrawler-mysql:15.01.06` | redg-examples/redg-example-mysql |
| MariaDB² | `us.fatehi:schemacrawler-mariadb:15.01.06` | No example, untested, probably like MySQL |
| PostgreSQL | `us.fatehi:schemacrawler-postgresql:15.01.06` | redg-examples/redg-example-postgres |
| Sybase IQ | `us.fatehi:schemacrawler-sybaseiq:15.01.06` | No example, support untested |

¹ <sub><sup>You need to [turn `QUOTED_IDENTIFIER` on](https://docs.microsoft.com/en-us/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=sql-server-2017) or change the character to escape identifiers from double quotes to backticks by customizing the `JavaSqlStringEscapeMap`.</sub></sup><br>
² <sub><sup>You need to either [enable `ANSI` mode](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html) for the database or the connection or change the character to escape identifiers from double quotes to backticks by customizing the `JavaSqlStringEscapeMap`.</sub></sup>

After the code is generated, it can be used to insert data into basically every DBMS with proper SQL support. When you insert the data directly
with JDBC, the driver needs proper support for `setObject`, when generating SQL statements you might have to implement your own `InsertValuesFormatter`,
so RedG uses the correct functions for e.g. string to date conversion.

If you run into issues with a certain DBMS or just want to tell us that it works, feel free to open an issue or contact us.