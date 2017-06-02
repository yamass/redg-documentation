description: Documentation for the explicit attribute and foreign key feature for the RedG code generator
path: tree/master/redg-generator/src/main/java/com/btc/redg/generator/extractor/explicitattributedecider
source: ExplicitAttributeDecider.java
        DefaultExplicitAttributeDecider.java
        JsonFileExplicitAttributeDecider.java
        
# Explicit attributes & foreign keys

Explicit attributes and explicit foreign keys can be used to "mark" certain attributes / foreign keys as required. 

If you mark a foreign key as explicit it is treated as a `#!sql NOT NULL` foreign key. This allows you to treat foreign keys that are meant to be `#!sql NOT NULL` 
but are nullable for some reason (legacy schema, cyclic dependencies, whatever) as `#!sql NOT NULL` within RedG and make them required for entity creation.

If you mark an attribute as explicit, it gets treated like a `#!sql NOT NULL` foreign key. This means, that a value has to be set for this attribute at
entity creation time and no default value will be used for this attribute. Use this feature sparingly, as using it everywhere defeats the purpose of
RedGs default value system.

## JSON file

Load your JSON files with the `JsonFileExplicitAttributeDecider`. 

The structure of the JSON input can be found below. 

!!! note "Regex support"
    All strings in this JSON file will be evaluated as regualr expressions and matched against the table / column / foreign key names. Remember to escape
    certain characters with a backslash ("\") if you want the literal character to match.
     
````json
{
  "TABLENAME": {
    "attributes": ["ATTRIBUTE1", "ATTRIBUTE2"],
    "relations": [
      ["FOREIGN_KEY_1_COLUMN_1", "COLUMN2"],
      ["FOREIGN_KEY_2_PART_1", "OTHER_PART2"]
    ]
  }
}
````

## Java API

If you want to roll your own implementation, simply implement `ExplicitAttributeDecider` and decide for each column / attribute and foreign key whether it
 should be explicitly required or not.

````java
public interface ExplicitAttributeDecider {

    boolean isExplicitAttribute(Column column);

    boolean isExplicitForeignKey(ForeignKey foreignKey);

}
````