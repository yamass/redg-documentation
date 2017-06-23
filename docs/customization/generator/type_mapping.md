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

The syntax for the JSON file is simple: You can specify `tableMappings` that specify the type for a column inside a table
or you can use the `defaultMappings` where you can assign a data type to be used for a specific SQL type. The `tableMappings` have a higher priority and can be 
used to override the `defaultMappings`.
 
For the `tableMappings`: Each table gets an own object. The key for that object is the full table name 
(including the schema name). This object contains a key for each column you want to map. The key is the column name. 
The value for each key is the wanted data type.

For the `defaultMappings`: The key is the SQL data type (with or without precision information), the value is the Java type. SQL types with precision information
always take precedence over types without precision information. So when a column has the data type `NUMBER(1)` and the mappings contain both `NUMBER -> Long` and
`NUMBER(1) -> Boolean` the column will be mapped to a `Boolean` type. When dealing with `VARCHAR`s or similar, you can also use "precision" (aka length) information,
but do not include `CHAR` inside the brackets (eg. if type is `VARCHAR2(100 CHAR)`, use `VARCHAR2(100)` to match this).

**Example:**
````json
{
  "tableMappings": {
    "SCHEMA-NAME.TABLE-NAME": {
      "COLUMN-NAME": "your.java.Datatype",
      "OTHER-COLUMN": "other.Type"
    },
    "SCHEMA-NAME.OTHER-TABLE": {
      "ID-COLUMN": "long"
    }
  },
  "defaultMappings": {
    "NUMBER": "java.lang.Long",
    "NUMBER(1)": "java.lang.Boolean"
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

The other allowed child element is `#!xml <defaultTypeMappings>`. It can have multiple `#!xml <type>` children with a `sql` attribute specifying the SQL data 
type (with precision support just like for JSON). The value of this node is the Java type that the SQL type should be mapped to.

**Example:**
````xml
<typeMappings>
   <tableTypeMappings>
       <table name="TABLE_NAME">
            <column name="COLUMN_NAME">java.lang.String</column>
       </table>
   </tableTypeMappings>
   <defaultTypeMappings>
       <type sql="DECIMAL(1)">java-lang.Boolean</type>
   </defaultTypeMappings>
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