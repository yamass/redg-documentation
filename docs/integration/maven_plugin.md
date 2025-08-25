description: Documentation for the RedG Maven plugin
path: tree/main/redg-maven-plugin/src/main/java/de/yamass/redg/plugin
source: RedGGeneratorMojo.java

# RedG Maven plugin

The RedG Maven plugin offers comfortable and easy integration of the RedG code generation into your 
Maven project. It is configurable via Maven properties and JSON files.

!!! note ""
    While the Maven plugin is pretty flexible, RedG can be configured even further if you use the 
    code generator API directly. If you need absolute control, check out the 
    [docs for the code generator API](generator_api.md).
    
## Usage

Simply add the RedG plugin into your `#!xml <plugins>` section of your `pom.xml`. You may choose your own
`#!xml <id>`, but the `#!xml <phase>` should stay at `generate-test-sources` in most cases. Now add your 
configuration in the `#!xml <configuration>` part and, if necessary, specify a dependency to your JDBC
driver. If you use a different RDBMS than H2 you need an extra dependency to the SchemaCrawler plugin for your DBMS.

The list of the most commonly needed plugins (for full list, see in [Maven Central](https://mvnrepository.com/artifact/us.fatehi)):

| DBMS | Necessary dependency |
| ---- | -------------------- |
| Oracle | `us.fatehi:schemacrawler-oracle:15.01.06` |
| IBM DB2 | `us.fatehi:schemacrawler-db2:15.01.06` |
| MS SQL Server | `us.fatehi:schemacrawler-sqlserver:15.01.06` |
| MySQL | `us.fatehi:schemacrawler-mysql:15.01.06` |
| PostgreSQL | `us.fatehi:schemacrawler-postgresql:15.01.06` |


```xml
<plugin>
    <groupId>de.yamass.redg</groupId>
    <artifactId>redg-maven-plugin</artifactId>
    <version>2.0</version>
    <executions>
        <execution>
            <id>redg-generate</id>
            <phase>generate-test-sources</phase>
            <goals>
                <goal>redg</goal>
            </goals>
            <configuration>
                <!-- RedG configuration goes here -->
            </configuration>
        </execution>
    </executions>
    <dependencies>
        <!-- JDBC driver and SchemaCrawler plugin, only if not using H2. Entry depends on DBMS -->
    </dependencies>
</plugin>
```

## Configuration

The following table lists all configuration options of the RedG Maven plugin.

| XML Tag | Default value | Explanation |
| ------- | ------------- | ----------- |
| `<connectionString>` | `jdbc:h2:mem:redg;DB_CLOSE_DELAY=-1` | The JDBC connection string for the database that will be analyzed. Uses a H2 in-memory database by default, so if you are using SQL-Scripts, you can omit the connection paramters most of the time. |
| `<username>` | | The username for the database.  Can be left unchanged if used with the default connection string. |
| `<password>` | | The password for the database user. Can be left unchanged if used with the default connection string. |
| `<jdbcDriver>` | `org.h2.Driver` | The JDBC driver. A Maven dependency providing this class has to be specified if you are not using H2. |
| `<sqlScripts>` | | A file array with SQL scripts that should be executed before the database analysis. These scripts can be used to create the schema when you are using an in-memory H2 database. |
| `<schemaRegex>` | `.*` | A regular expression to select all database schemas that will be included in the database analysis. |
| `<tablesRegex>` | `.*` | A regular expression to select all the tables that will be included in the database analysis. If a table is excluded because it belongs to an exlcuded schema, it will **not** be included again. |
| `<outputDirectory>` | `target/generated-test-sources/redg` | The output folder for the generated source code. Default value is Maven standard. |
| `<targetPackage>` | `de.yamass.redg.generated` | The java package of the to-be-generated entity classes. |
| `<classPrefix>` | `G` | A prefix string that gets prepended to each entity class. |
| `<allowPrimitiveTypes>` | `false` | By default RedG replaces primitive types with their wrapper classes (`int` to `Integer`, etc.). Set to `true` to disable this behavior. |
| `<enableVisualizationSupport>` | `false` | If `true`, the generated code will support [visualization](../features/visualization.md) of the object graph |
| `<customTypeMappings>` | | A JSON or XML file with custom type mappings. See [here](../customization/generator/type_mapping.md) for an explanation and an example code.|
| `<customNameMappings>` | | A JSON file that defines custom name mappings. See [here](../customization/generator/name_mapping.md#json-file) for an explanation and an example JSON code.|
| `<explicitAttributesConfig>` | | A JSON file that defines explicit attributes and foreign keys. See [here](../customization/generator/explicit_attributes.md#json-file) for an explanation and an example JSON code.|
| `<convenienceSetterConfig>` | | A XML file that defines convenience setter methods. See [here](../customization/generator/convenience_setters.md#xml-file) for an explanation and an example XML code.|
| `<jpaProviderConfig>` | | The configuration for the JPA meta-model analysis. See [here](../features/jpa_metamodel.md) for an explanation of this feature and example XML. 

When running maven from the shell, you can use the follwing command line properties to override some configuration values:

| XML Tag | CMD property |
| ------- | ------------ |
| `<connectionString>` | `redg.connectionString` |
| `<username>` | `redg.username` |
| `<password>` | `redg.password` |
| `<jdbcDriver>` | `redg.jdbcDriver` |
| `<enableVisualizationSupport>` | `redg.enableVisualization` |