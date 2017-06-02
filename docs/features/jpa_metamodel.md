# JPA meta-model analysis

RedG offers the ability to analyze a JPA meta-model and extract the names and data types from there. So if you are using a
JPA persistence layer, configuring the names and data types with your existing JPA meta-model might be considerably easier
for you.

## Maven plugin configuration

To use the meta-model analysis with the [Maven plugin](../integration/maven_plugin.md), add the following XML to the 
plugin configuration:

````xml
<jpaProviderConfig>
    <persistenceUnitName>nameOfPersistenceUnit</persistenceUnitName>
    <useAsNameProvider>true</useAsNameProvider>
    <useAsDataTypeProvider>false</useAsDataTypeProvider>
</jpaProviderConfig>

````

## Java API

Create a new `JpaMetamodelRedGProvider` either with a `Metamodel` via its constructor or with the static method
`JpaMetamodelRedGProvider#fromPersistenceUnit(String perstistenceUnitName)`. Now use it as the name provider or data type
provider as parameter for the RedG code generator method.