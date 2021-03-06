<p align="center">
  <img src="https://github.com/angeliferreira/environment/blob/master/environment.jpg?raw=true"/>
</p>

# environment [![Build Status](https://travis-ci.org/angeliferreira/environment.png?branch=master)](https://travis-ci.org/angeliferreira/environment)

Environment is a basic project for running set ups before running each Java automated test.


## Maven integration

To integrate *Environment* to your Maven project, you must declare the following dependency (Not in maven repository yet, must install it local):

```xml
<dependency>
    <groupId>br.com.lemao</groupId>
    <artifactId>environment</artifactId>
    <version>1.0</version>
    <scope>test</scope>
</dependency>
```

## Getting Started

## Basic Structure

### Creating your environment class

The abstract class *Environment* is the superclass for the Environments structure, where you gonna put your data creation calls. 

It supports 2 different basic usages, by class or by method.

### Structure of environment per class

The first implementation would provide one environment abstraction per class. You must implement the *run()* method only.

You have two auxiliary methods *beforeRun()* and *afterRun()* that are intended to provide creation and dispose of resources 
before and after executing the *run()* method, implementing these methods is optional, and the *afterRun()* method will be 
executed even if *run()* throws exception.

Your implementation, using the class Environment structure would be as follows:

```java
public class SampleEnvironment extends Environment {

   @Override
   public void run() {
      SampleUtil.createSample();
   }

}
```

Your implementation using *beforeRun()* and *afterRun()* would be as follows:

```java
public class SampleEnvironment extends Environment {

    @Override
    public void run() {
        SampleUtil.createSample();
    }

    @Override
    public void afterRun() {
        SampleUtil.afterCreateSample();
    }

    @Override
    public void beforeRun() {
        SampleUtil.beforeCreateSample();
    }
}
```

### Structure of environment per method

The second implementation would provide one environment abstraction per method. You must implement different methods as you will. In this case you must create an Environment class which comprises the related methods.

Your implementation, using the method Environment structure would be as follows:

```java
public class SampleEnvironment extends Environment {

   public void myEnvironmentMethodNameOneSample() {
      SampleUtil.createSample();
   }

   public void myEnvironmentMethodName2Samples() {
      SampleUtil.create2Samples();
   }

}
```

## Creating Environments hierarchically

It is possible to build a structure of hierarchical environments. You only need annotate the Environment method with @GivenEnvironment passing the Environment you want as a parent.

### @GivenEnvironment annotation

The annotation @GivenEnvironment tells which structure will be executed before the method.

The GivenEnvironment annotation supports two basic uses.

The first use you only have to pass as parameter the Environment class which has a run() method, structure Environment per class.

Its use in a Structure of environment per class would be as follows:

```java
public class SampleEnvironmentOneSample extends Environment {

   @Override
   public void run() {
      SampleUtil.createSample();
   }

}

public class SampleEnvironment3Samples extends Environment {

   @Override
   @GivenEnvironment(SampleEnvironmentOneSample.class)
   public void run() {
      SampleUtil.create2Samples();
   }

}
```

The second use you have to pass as parameter the Environment class and the Environment method, structure Environment per method.

Its use in a Structure of environment per method would be as follows:

```java
public class SampleEnvironment extends Environment {

   public void myEnvironmentMethodNameOneSample() {
      SampleUtil.createSample();
   }

   @GivenEnvironment(value=EnvironmentSample.class, environmentName="myEnvironmentMethodNameOneSample")
   public void myEnvironmentMethodName3Samples() {
      SampleUtil.create2Samples();
   }

}
```

## Executing your Environments

The only thing you have to do is get an EnvironmentExecutor and execute!

```java
public class Sample {

   @Test
   public void method() {
      EnvironmentExecutor.gimme().execute(EnvironmentSample.class); //It expects the method EnvironmentSample.run() to be implemented
	  org.junit.Assert.assertFalse(SampleUtil.findAll().isEmpty());
   }
   
   @Test
   public void method2() {
      EnvironmentExecutor.gimme().execute(EnvironmentSample.class, "environmentSampleMethodName"); //It expects to exists the method EnvironmentSample.environmentSampleMethodName()
	  org.junit.Assert.assertFalse(SampleUtil.findAll().isEmpty());
   }

}
```

## You must also see

REMEMBER, TESTS ARE DOCUMENTATION

* *_[Environment Example Tests](https://github.com/angeliferreira/environment/tree/master/src/test/java/br/com/lemao/environment/test)_*
* *_[Environment JUnit](https://github.com/angeliferreira/environment-junit)_*
* *_[SimulaTest - Simulatest Test Harness Framework](https://github.com/gabrielsuch/simulatest)_*
* *_[Fixture Factory](https://github.com/six2six/fixture-factory)_*
* *_[DbUnit](http://dbunit.sourceforge.net/)_*