# Integration

The way you integrate RedG into you project depends on your build system and personal preferences. RedG is very flexible and
can easily be adapted to fit your project.

## RedG code generator

The RedG code generator analyzes your database and generates matching entity classes you can then use to specify your test
data. Usually the code generation is so fast that it can be run before every test, thus ensuring it is always up-to-date and
matches the current database schema. 

There are multiple ways of integrating the code generator into your project.

 * The [RedG Maven plugin](maven_plugin.md)
 * Calling the [generator API](generator_api.md) manually

## RedG runtime

The RedG runtime library is available as a Maven dependency in the Maven Central. 

All of the following integration possibilities require the following dependency:
````xml
<dependency>
    <groupId>com.btc-ag.redg</groupId>
    <artifactId>redg-runtime</artifactId>
    <version>2.0</version>
    <scope>test</scope>
</dependency>
````

After you have specified your test data you can insert them with the [runtime API](runtime_api.md). This approach
offers absolute flexibility and can easily be integrated into any Java project.