description: Documentation for convenience setter method feature for the RedG code generator
path: tree/main/redg-generator/src/main/java/de/yamass/redg/generator/extractor/conveniencesetterprovider
source: ConvenienceSetterProvider.java
        DefaultConvenienceSetterProvider.java
        xml/XmlFileConvenienceSetterProvider.java

# Convenience setter methods

This feature of RedG allows you to add convenience setters to the generated entity classes.
For example an entity with a `java.sql.Timestamp` field could get an extra setter for better date types 
(like `java.time.LocalDatetime`). All you have to provide is a conversion method that can convert from the convenient 
data type to the needed type. This conversion method needs to be a public static method taking exactly two parameters:

 * The value for the attribute (as the convenient type)
 * The class (`Class<?>`) of the required type
 
It needs to return an instance of the class that was passed as a second parameter. Using generics, the method 
signature for a conversion method that transforms a string into some kind of date could be 
`#!java public static <T> T convertToDate(String s, Class<T> clazz)`.

The RedG runtime provides a converter that can convert a ISO-8601 formatted string into basically every Java date type
(`java.util.Date, java.sql.Date, Time, Timestamp, LocalTime, LocalDate, LocalDateTime, ZonedDateTime, OffsetDateTime, OffsetTime`).
To use it, specify `de.yamass.redg.runtime.util.DateConverter.convertDate` as the fully qualified converter method name.

## XML file

Use the `XmlFileConvenienceSetterProvider` class to load your XML file.

In the XML you can specify multiple convenience setters for each original data type. The example XML specifies that every
attribute of the type `java.util.Date` gets another setter accepting a `java.lang.String` and converting this string into a
date with the `de.yamass.redg.runtime.util.DateConverter.convertDate` method.

If you want to restrict the convenience setters to certain tables / columns, you have to provide a 
[custom implementation](#java-interface).

````xml
<convenienceSetterConfig>
   <javaType name="java.util.Date">
       <convenienceSetter setterJavaTypeName="java.lang.String" fullyQualifiedConverterMethodName="de.yamass.redg.runtime.util.DateConverter.convertDate"/>
   </javaType>
</convenienceSetterConfig>
````

## Java API

Your custom implementation has to implement the `ConvenienceSetterProvider` interface. You can then decide for each 
column on every table what convenience setters should be added. If you do not want any convenience setters, return an 
empty list or fall back to a `DefaultConvenienceSetterProvider`. Please always use the `javaDataTypeName` parameter as
the required parameter type and not the type according to the `column`, as your [type mapping](type_mapping.md) might
have changed the type that the database analysis framework suggested.

````java
public interface ConvenienceSetterProvider {

    List<ConvenienceSetterModel> getConvenienceSetters(Column column, String javaDataTypeName);

}
````