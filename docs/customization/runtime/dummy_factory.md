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

## Implement own DummyFactory

If you need to, you can always implement your own `DummyFactory`. Here are a few tips when implementing your custom factory:

 * Remember to take care of all transitive dependencies.
 * Abstain from using user-generated entities already within RedG as dummies. Dummies should be entities your factory generated.
 * Implement `isDummy` correctly, or features like the visualization will not work properly.
 
````java
public interface DummyFactory {

    <T extends RedGEntity> T getDummy(AbstractRedG redG, Class<T> dummyClass);

    boolean isDummy(RedGEntity entity);
}
````