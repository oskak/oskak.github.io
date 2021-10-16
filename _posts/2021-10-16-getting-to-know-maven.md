---
title: Getting to know Maven
tags: [Maven]
---

## What is Maven?
Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information. See [Welcome to Apache Maven](https://maven.apache.org).

## Introduction to the POM
A Project Object Model or POM is the fundamental unit of work in Maven. It is an XML file that contains information about the project and configuration details used by Maven to build the project. It contains default values for most projects. Examples for this is the build directory, which is `target`; the source directory, which is `src/main/java`; the test source directory, which is `src/test/java`; and so on. When executing a task or goal, Maven looks for the POM in the current directory. It reads the POM, gets the needed configuration information, then executes the goal. See [Introduction to the POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

## Creating a Project
On your command line, execute the following Maven goal:
```
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

You will notice that the generate goal created a directory with the same name given as the artifactId:
```
my-app
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- com
    |           `-- mycompany
    |               `-- app
    |                   `-- App.java
    `-- test
        `-- java
            `-- com
                `-- mycompany
                    `-- app
                        `-- AppTest.java
```

The `src/main/java` directory contains the project source code, the `src/test/java` directory contains the test source, and the `pom.xml` file is the project's Project Object Model, or POM.

## Maven Phases
Although hardly a comprehensive list, these are the most common default lifecycle phases executed:
- validate: validate the project is correct and all necessary information is available
- compile: compile the source code of the project
- test: test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
- package: take the compiled code and package it in its distributable format, such as a JAR.
- integration-test: process and deploy the package if necessary into an environment where integration tests can be run
- verify: run any checks to verify the package is valid and meets quality criteria
- install: install the package into the local repository, for use as a dependency in other projects locally
- deploy: done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.

There are two other Maven lifecycles of note beyond the default list above. They are:
- clean: cleans up artifacts created by prior builds
- site: generates site documentation for this project

## What happens when I add a Maven dependency?
```
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.4</version>
</dependency>
```

`groupId`, `artifactId` and `version` are 3 keys by which a jar is uniquely identified. 

Whenever you issue a `mvn package` command Maven tries to add the the jar file indicating by the dependency to you build path. For doing this Maven follows these steps:
- Maven search in your local repository (default is `~/.m2` for linux);
- If the dependency is not found in `~/.m2` then it looks for your local private repository or Maven central remote repository;
- Whenever the jar is found in the local/remote repository it is downloaded and saved in `~/.m2`.

## What is a JAR?
JAR 包就是别人已经写好的一些类，然后将这些类进行打包。你可以将这些 JAR 包引入你的项目中，然后就可以直接使用这些 JAR 包中的类和属性以及方法。同 Java 中其他任何东西一样，JAR 文件是跨平台的。

## Download Maven
Via Homebrew:
```
brew install maven
```

Check version:
```
mvn -v
```

## References
- [Maven in 5 Minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
- [Introduction to the POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)
- [What happens when I add a Maven dependency?](https://stackoverflow.com/questions/29614934/what-happens-when-i-add-a-maven-dependency)
