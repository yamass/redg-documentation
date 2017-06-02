# Compatibility

RedG is able to work with the most common database systems. All you need is a JDBC driver.
The Code Generator internally uses [SchemaCrawler](http://sualeh.github.io/SchemaCrawler) which supports a 
[wide range of database systems](http://sualeh.github.io/SchemaCrawler/database-support.html). You'll probably need an extra 
SchemaCrawler plugin, so consult the table below or see [Maven Central](https://mvnrepository.com/artifact/us.fatehi) for a full list.

| DBMS | Necessary extra dependency | Example project |
| ---- | -------------------- | --------------- |
| H2 | Dependency included in RedG | redg-examples/redg-example-h2 | 
| Oracle | `us.fatehi:schemacrawler-oracle:14.16.01` | redg-examples/redg-example-oracle |
| IBM DB2 | `us.fatehi:schemacrawler-db2:14.16.01` | No example, support untested |
| MS SQL Server | `us.fatehi:schemacrawler-sqlserver:14.16.01` | No example, support untested |
| MySQL | `us.fatehi:schemacrawler-mysql:14.16.01` | redg-examples/redg-example-mysql |
| MariaDB | `us.fatehi:schemacrawler-mariadb:14.16.01` | No example, untested, probably like MySQL |
| PostgreSQL | `us.fatehi:schemacrawler-postgresql:14.16.01` | redg-examples/redg-example-postgres |
| Sybase IQ | `us.fatehi:schemacrawler-sybaseiq:14.16.01` | No example, support untested |

After the code is generated, it can be used to insert data into basically every DBMS with proper SQL support. When you insert the data directly
with JDBC, the driver needs proper support for `setObject`, when generating SQL statements you might have to implement your own `InsertValuesFormatter`,
so RedG uses the correct functions for e.g. string to date conversion.

If you run into issues with a certain DBMS or just want to tell us that it works, feel free to open an issue or contact us.