description: Documentation for the DummyFactory of the RedG runtime, how it can be extended or replaced
path: tree/master/redg-runtime/src/main/java/com/btc/redg/runtime/dummy
source: DummyFactory.java
        DefaultDummyFactory.java
        DummyPostProcessor.java

# Dummy factory

This section describes how to modify the default behaviour of RedG's dummy data generation mechanism. For an explanation 
and a guide on how to use this mechanism, check [this](../../features/dummy_data.md) out.

There are two main reasons to modify or extend the existing dummy generation system:
 
 * You want to modify the generated dummy entities
 * RedG cannot generate the dummy entities for some reason
 
If the latter occurs, feel free to open an [issue](https://gitlab.com/btc-redg/redg/issues/new) and we might try to 
implement support for your case.

## Modify generated dummy entities

When you are using the `DefaultDummyFactory`, you can add `DummyPostProcessor`s. Each post-processor can modify 
(or even replace) the dummy entity before it is inserted into RedG's entity store and returned from the 
`getDummyX()` method.
 
Yo can add a post-processor with the `addDummyPostProcessor` method of the `DefaultDummyFactory`.

The `DummyPostProcessor` interface is incredibly simple:
````java
public interface DummyPostProcessor {
    <T extends RedGEntity> T processDummy(T entity);
}
````

## Implement own DummyFactory

If you need to, you can always implement your own `DummyFactory`. Here are a few tips when implementing your custom factory:

 * Remember to take care of all transitive dependencies.
 * Abstain from using user-generated entities already within RedG as dummies, as the post-processors might change them.
 * If you do not support `DummyPostProcessor`s, please throw an `UnsupportedOperationException` and do not silently discard the post-processor. This might bewilder users who expect their post-processors to be called.

````java
public interface DummyFactory {

    <T extends RedGEntity> T getDummy(AbstractRedG redG, Class<T> dummyClass);

    void addDummyPostProcessor(DummyPostProcessor dummyPostProcessor);
    void removeDummyPostProcessor(DummyPostProcessor dummyPostProcessor);
    void removeAllDummyPostProcessors();
}
````