description: Documentation for the code generator API
path: tree/main/redg-generator/src/main/java/de/yamass/redg/generator
source: RedGGenerator.java

# Code generator API

When you need every last bit of customizability, using the code generator API is your best option. It offers you
one big `generateCode` method that gives you access to every bit of customization that RedG supports out-of-the-box.

To use the API, simply include the following Maven dependency:
````xml
<dependency>
    <groupId>de.yamass.redg</groupId>
    <artifactId>redg-generator</artifactId>
    <version>2.0</version>
</dependency>
````

If you use are not using H2, you need to inlcude the JDBC driver and the SchemaCrawler plugin for your DBMS. See [here](../compatibility.md) for a
list of all supported DBMS and their SchemaCrawler and plugins.

See the [Javadoc](../../apidocs) or source code for a detailed description of every parameter of the `generateCode` method.
If you like an example how to use this method, take a look at the 
[Maven plugin source](https://github.com/yamass/redg/tree/main/redg-maven-plugin/src/main/java/de/yamass/redg/plugin/RedGGeneratorMojo.java).

!!! note ""
    If even the standard API is not enough, take a look at the `RedGGenerator` source code. RedG is pretty modular
    and every important method is public, so you can just re-use the parts that work for you and re-implement the other parts.
    
    If your extension could benefit the community, please consider publishing it and create a pull request.