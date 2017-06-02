description: Documentation for the custom name mapping for the RedG code generator
path: tree/master/redg-generator/src/main/java/com/btc/redg/generator/extractor/nameprovider
source: NameProvider.java
        DefaultNameProvider.java
        MultiProviderNameProvider.java
        json/JsonFileNameProvider.java

# Custom name mapping

The custom name mapping allows you to change how RedG names the entity classes and their methods. In many cases and when using pretty default SQL names no
intervention is needed here.

The default naming behaviour of RedG is described below:

| Type | Behaviour | Example |
| ---- | --------- | ------- |
| Tables | Split by underscore into words. Make words lowercase with first letter uppercase and join them together, thus creating a uppercase camel-case name. | DEMO_USER --> DemoUser |
| Columns | Split by underscore into words. Make words lowercase with first letter uppercase and join them together. First word starts lowercase, thus creating a lowercase camel-case name. | FIRST_NAME --> firstName |
| Foreign Key | Split by underscore into words. Remove "FK" and name of own table. Build lowercase camel-case name of remaining words. | FK_USER_WORKS_AT_COMPANY --> worksAtCompany |
| Incoming Foreign Key | Name of the referencing table + "sFor" + Name for foreign key | |

## JSON file

Use the `JsonFileNameProvider` when specifying name mappings in a JSON file. The [Maven plugin](../../integration/maven_plugin.md) has built-in support for JSON
files. Getting names for incoming foreign keys from JSON files is not supported yet and RedG will fall back to the `DefaultNameProvider`.

See below for the syntax of the JSON file:
````json
{
  "TABLE_NAME": {
    "name": "TableClassName",
    "columns": {
      "COLUMN_1": "myCustomNameCol1",
      "COLUMN_2": "otherCustomName",
      "FK_SPECIAL_FOREIGN_KEY": "customFkName"
    }
  }
}
````


## Java API

Custom implementations have to implement the `NameProvider` interface. If you only want to override behaviour for one type (table, column, foreign key), consider
returning `null` for all methods you do not want to override and use a `MultiProviderNameProvider`.

````java
public interface NameProvider {

    String getClassNameForTable(Table table);

    String getMethodNameForColumn(Column column);

    String getMethodNameForForeignKey(ForeignKey foreignKey);

    String getMethodNameForIncomingForeignKey(ForeignKey foreignKey);
}
````