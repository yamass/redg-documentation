# Quick Start Guide

This is a quick start guide for the development of RedG. If you want to use RedG in your project, see [here](../getting_started.md).

!!! info
    RedG was developed for internal usage in projects of the BTC Business Technology Consulting AG.
    Further development will mainly be driven by the needs of the internal projects using RedG.
    If you require a feature, either open an issue and hope it might get implemented or feel free to 
    fork our repository, implement it yourself and create a pull request.
    
## Project setup

Clone the Git-Repository (`git@github.com:btc-ag/redg.git` or your HTTPS link). RedG uses Maven 
for development and you can simply import the main `pom.xml` in your favorite IDE.

The main project has 6 sub-projects:

| Module | Content |
| ------ | ------- |
| `redg-extractor` | Library for generating RedG entity definition code from existing entities in a database. Can be used for migrating to RedG |
| `redg-generator` | Analyzes the database schema and generates the matching entity classes. Search here if you want to extend or have trouble with the generated code |
| `redg-jpa-providers` | A name and datatype provider for usage with the `redg-generator`, that uses information from your JPA Metamodel. Not included by default because of Hibernate dependency |
| `redg-maven-plugin` | The plugin that integrates the `redg-generator` into the Maven build process |
| `redg-models` | Common model files |
| `redg-runtime` | The runtime needed during execution. Search here, if the generated code is fine but execution fails during runtime or you want more features |