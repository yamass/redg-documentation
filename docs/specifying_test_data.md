# Specifying test data

With RedG you specify all your test data in pure Java code. And "all your test data" means 
"only the data you *really* need". RedG takes care of all the other stuff (like not specified `NOT NULL` fields, required
foreign key relations etc.).

## General API design

The entity classes RedG generates use a [fluent interface](https://en.wikipedia.org/wiki/Fluent_interface) with getters and
setters in "JQuery style" (no `get`/`set` prefix, calling without parameter gets the value, with parameter sets it).

RedG  always generates a "main class" (or "manager class"). This is not an executable class containing a 
`public static void main(String[] args)`, but the class you'll use to create and manage your RedG entities (and thus your
test data). This class is usually named `RedG`.

Using the RedG main class you can create entities. An entity is a java object that represents one row of data that will be
inserted into a database (or already [exists in the database](#referencing-existing-entities)). Usually you can use methods
to change fields ("columns" in your database) on these entities.

## Adding an entity

To create a new entity and add it to RedG's internal list of objects to insert call one of the `add...()` methods
on a RedG object. Replace `...` with the name of the entity you want to add. If the entity has required fields (either
`NOT NULL` foreign keys or columns / foreign keys marked as [explicit](customization/generator/explicit_attributes.md)) 
you'll need to pass them to the method. Passing a `null` value will result in a `NullPointerException`.

Once you have created your entity you can use it's setters to set the values for the necessary fields.

Example:
````java

RedG redG = new RedG();

// either 
GTeacher mathTeacher = redG.addTeacher()
    .name("Leonhard Euler")
    .age(310);

// or just
redG.addTeacher()
    .name("Isaac Newton");

// or even
GTeacher chemistryTeacher = redG.addTeacher();
chemistryTeacher
    .name("Niels Bohr")
    .hasNobelPrice(true)
````

When you do not need an entity for further foreign key relations or data manipulation, there is no need to save it 
in a variable.

As you can see in the example, different fields are set for `mathTeacher` and `chemistryTeacher` although they are of the same 
type and have the same columns. Even if `age` or `hasNobelPrice` would both be mandatory (`NOT NULL`) this code would
still work, as RedG generates default values for all fields that are not specified by the user.

Example for entities with `NOT NULL` foreign key or explicit attributes:

````java
// name of school is an explicit attribute for demonstration purposes
redG.addSchool("Time-traveling School with famous teachers")
    .headmaster(einstein);

// Every class needs a teacher, so it is specified at creation time and may not be null
GClass chemistryClass = redG.addClass(chemistryTeacher)
    .name("Chemistry 101")
    .maxStudentCount(45)
````

## Generating a dummy entity

There are many scenarios where you need to test a specific entity but only need some or none of its dependencies.
With most other tools you would have to specify each and every dependency (and their dependencies and so on) and 
you could wind up with far too much code/XML for just one entity.

To solve this problem RedG offers a dummy entity generator, that will generate a valid dummy entity for you. This entity
will have meaningless values but is still a valid entity that can be referenced wherever you want.

To stay in the educational environment with this example, imagine a test case where you need to test the `SCHOOL_SUBJECT` table
(`SchoolSubject` entity name, `GSchoolSubject` entity class name). Every subjects needs a teacher. A teacher is a human
with a pay grade, a qualification and a school he graduated from (All of these have to be provided because of constraints 
on the database). Every subjects needs one required textbook as well.

````java
// Without dummy entities and without the previously defined entities

GSchool school = redG.addSchool("Wherever Primary School");
GHuman somePerson = redG.addHuman();
GTeacher someTeacher = redG.addTeacher(somePerson, Paygrade.TEACHER, 
    Qualifications.NONE, school);
GTextBook textBook = redG.addTextBook();

GSchoolSubject aqm = redg.addSchoolSubject(someTeacher, textBook)
    .name("Advanced quantum mechanics")
    .isCoreClass(true)
    .room("1.42");

// with RedGs dummy generator

GSchoolSubject aqm = redG.addSchoolSubject(
        redG.dummyTeacher(),
        redG.dummyTextBook()
)
    .name("Advanced quantum mechanics")
    .isCoreClass(true)
    .room("1.42");

````

## Referencing existing entities

Depending on your setup you might already have some data in your database before running RedG and need to reference
these data for a foreign key relation within RedG.

RedG allows you to specify that a certain entity is already present in the database. When inserting you test data
RedG will test that the specified entity is really already in the database.

To reference an existing entity, use the `existing...()` methods of the RedG object. You have to pass
the full primary key as the arguments to these methods. If a table does not have primary keys, you cannot reference
it.

Example:
````java
GTeacher euklid = redG.existingWizard(4); // 4 is value of primary id column

// use euklid just like normal for references, just don't try to modify him or read values other the primary keys

// trying to modify fields will throw an UnsupportedOperationException
euklid.isDead(false); 

redG.addMemorialDay(euklid) // works just like expected
````
