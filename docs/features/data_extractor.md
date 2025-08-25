# Data extractor

To help you with migrating from your existing solution to RedG you can use
the RedG extractor. It enables you to extract all the data from a database and
create a RedG data set that, if inserted, will produce the exact same database
content.

!!! note
    The RedG data set will contain every column from every table. This does not
    make use of RedG's greatest features (default values, dummy generator).
    Use the extraction result as a starting point and then reduce your data to the
    needed minimum. This has to be done by hand, as RedG does not know what data you
    really need.
    
## Java API

If you have your current test data only available in-memory during your tests,
use the Java API of the extractor to generate the RedG Java code.

The generation is a three-step process:

 1. Get the RedG `TableModel`s for all tables you want to extract
 2. Extract all the data into `EntityModel`s
 3. Generate the code for the `EntityModel`s
 
### Obtain the TableModels

There are multiple ways of obtaining the `TableModel`s for your database schema.
RedG writes a serialized table model into every generated entity class code.

These can be accessed at runtime via the static `getTableModel()` method.
If the generated classes are either not yet compiled or compiled but not loaded
in your current JVM instance, you can use the `TableModelExtractor.extractTableModelsFromSourceCode(directory, codePackage, classPrefix)`
or `TableModelExtractor.extractTableModelFromClasses(directory, codePackage, classPrefix)` methods to
extract the `TableModel`s for all RedG entity classes.

These two methods take three parameters:

| Parameter | Explanation |
| --------- | ----------- |
| `directory` | The `Path` of the source code root, **without** the package structure folders |
| `packageName` | The name of the Java package that was used during code generation. `de.yamass.redg.generated` if not overwritten |
| `classPrefix` | The class name prefix for the generated RedG classes. `G` by default |

### Extract all data

Obtain a connection to your database and call `new DataExtractor().extractAllData( connection, tableModels)`.
`tableModels` should be the list of `TableModel`s from the previous step.

!!! note
	If the database you are extracting from is not the same you generated the RedG code from, you might have to change the schema name.
	To do so, use `DataExtractor#setSqlSchema(String schema)` to specify the schema name.

Save the result in a list of `EntityModel`s for the next step.

!!! note
    If you manually exclude some `TableModel`s, keep in mind that the extraction will fail if a `TableModel`
    for a dependend table (via foreign key) is not found.

### Generate the code

Using the `EntityModel`s you can now call `new CodeGenerator().generateCode( codePackageName, redGClassName, codeClassName, entityModels)`

This will return the Java code as a String. The generated code will be in the default package, so you might need to add your own `package x.y.z` statement at the top.

The `generateCode` method takes four parameters:

| Parameter | Explanation |
| --------- | ----------- |
| `codePackage` | The code package of the generated RedG entity classes. Needed for imports |
| `redgClassName` | The name of your RedG main class. Normally `RedG` |
| `className` | The name that will be used for the class containing your entity specification code |
| `entityModels` | The `EntityModel`s from the previous step |

## Using the generated code

When your database only contains strings and numbers, RedG's extractor will most likely produce working Java code.
If you are using dates, blobs or other more "exotic" types that cannot simply be mapped to a Java type, the `toString()` result
will simply be used as a placeholder and you'll have to fix the code.
