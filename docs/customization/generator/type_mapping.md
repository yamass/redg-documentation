description: Documentation for the custom type mapping for the RedG code generator
path: tree/master/redg-generator/src/main/java/com/btc/redg/generator/extractor/datatypeprovider
source: DataTypeProvider.java
        DefaultDataTypeProvider.java
        json/JsonFileDataTypeProvider.java
        xml/XmlFileDataTypeProvider.java

#Custom type mapping

RedG allows you to customize the SQL to Java type mapping for the generated entity classes.
When analysing a database schema, RedG always tries to find an appropriate Java datatype for a column. 
There are three cases, where this might not be enough:

 - You want a different Java datatype that still represents the same database datatype (eg. `long` instead of `BigDecimal` for `NUMBER(10)`)
 - RedG cannot understand the semantic meaning of a column datatype (eg. `NUMBER(1)` or `CHAR(1)` for `boolean`)
 - You want to plug in a custom, maybe even far more complex, data type and use it instead of the default types

If this happens, you can create a custom type mapping. The following sections describe how to specify your custom mapping.

## JSON file

If you want to specify your mapping in a JSON file, use the `JsonFileDataTypeProvider`. The 
[Maven plugin](../../integration/maven_plugin.md) has built-in support for JSON files.

The syntax for the JSON file is simple: Each table gets an own object. The key for that object is the full table name 
(including the schema name). This object contains a key for each column you want to map. The key is the column name. 
The value for each key is the wanted data type.
````json
{
  "SCHEMA-NAME.TABLE-NAME": {
    "COLUMN-NAME": "your.java.Datatype",
    "OTHER-COLUMN": "other.Type"
  },
  "SCHEMA-NAME.OTHER-TABLE": {
    "ID-COLUMN": "long"
  }
}
````

## XML file

If you want to specify your type mapping with an XML file, use the `XmlFileDataTypeProvider`. The 
[Maven plugin](../../integration/maven_plugin.md) has built-in support for XML files. 

The root element is `#!xml <typeMappings>`. This element has a child `#!xml <tableTypeMappings>`. Each table you want to
specify the types for gets a `#!xml <table>` element with a `name` attribute specifying the table name. 
Each table element has `#!xml <column>` child elements. These elements have a `name` attribute and their value is
the wanted data type.

Future versions of RedG will allow more possibilities with XML mappings (mapping complete SQL types to different Java
data types).
````xml
<typeMappings>
   <tableTypeMappings>
       <table name="TABLE_NAME">
            <column name="COLUMN_NAME">java.lang.String</column>
       </table>
   </tableTypeMappings>
</typeMappings>
````

## Java API
Custom implementations have to implement the `DataTypeProvider` interface. The method `getCanonicalDataTypeName` gets
called for each column in every table. The method has to return a fully qualified class name that should be used for
that column.

````java
public interface DataTypeProvider {
    String getCanonicalDataTypeName(Column column);
}
````