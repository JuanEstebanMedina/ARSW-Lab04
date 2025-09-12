# ARSW-Lab04

**Colombian School of Engineering Julio Garavito**  
**Software Architectures - ARSW**  
**Laboratory Number 4**

**Members:**
- Juan Esteban Medina Rivas
- María Paula Sánchez Macías

---

## Part 0 - aditional basic exercise

> To illustrate the use of the Spring framework and the development environment for using it through Maven (and NetBeans), we will configure a text analysis application that uses a grammar checker that requires a spell checker. The required spell checker will be injected into the grammar checker at runtime (for now, there are two available: English and Spanish).

First, we had to open the project with NetBeans

<img src="excersice/img/1. NetBeans.png">

> Second, we checked that the Spring configuration file already included in the project (src/main/resources). It indicates that Spring will automatically search for the 'Beans' available in the specified package.

<img src="excersice/img/2. NetBeans.png">

<img src="excersice/img/2.1 base-package.png">

> Third, we had o use the annotation-based Spring configuration, mark the dependencies that must be injected with the @Autowired and @Service annotations, and the candidate beans to be injected, respectively.

- GrammarChecker will be a bean, which has a dependency of type ‘SpellChecker’.
- EnglishSpellChecker and SpanishSpellChecker are the two possible candidates to be injected. You must select one or the other, but NOT both (there would be a dependency resolution conflict). For now, use EnglishSpellChecker.

We use *@Service("spanishSpellChecker")* and *@Service("englishSpellChecker")* to solve the conflicts and then *@Qualifier("englishSpellChecker")*

<img src="excersice/img/3. Autowired.png">

> we created a test program where an instance of GrammarChecker is created using Spring, and then used:

```java
public static void main(String[] args) {
	ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
	GrammarChecker gc=ac.getBean(GrammarChecker.class);
	System.out.println(gc.check("la la la "));
}
```

> we modified the configuration with annotations so that the Bean ‘GrammarChecker’ now uses the SpanishSpellChecker class (so that GrammarChecker is injected with EnglishSpellChecker instead of SpanishSpellChecker). Verify the new result.

**Spanish Spell Checker Test**

<img src="excersice/img/4.1 spanishCheckerTest.png">

**English Spell Checker Test**

<img src="excersice/img/4.2 englishCheckerTest.png">


---

## Part I - Connectors and Components

> In this exercise, we will build a class model for the logical layer of an application that manages architectural plans for a prestigious design company.

<img src="img/ClassDiagram1.png">

> 1. Configure the application to run under a dependency injection scheme, as shown in the diagram above.

This means we should:

* Add Spring dependencies.

<img src="img/1. Dependencies.png">

* Add Spring configuration.

<img src="img/1.2 ApplicationContext.png">

* Configuring the application, using annotations, so that the persistence scheme is injected when the 'BlueprintServices' bean is created.

We add the *@Service* annotation to *BlueprintsService* and *InMemoryBlueprintPersistence*.

<img src="img/1.3 InMemoryBlueprintPersistence.png"> <img src="img/1.3 BlueprintsServices.png">

> 2. We complete the *getBluePrint()* and *getBlueprintsByAuthor()* operations. Implement everything required from the lower layers (for now, the available *InMemoryBlueprintPersistence* persistence scheme) by adding the corresponding tests in *InMemoryPersistenceTest*.

After the implementation, we runned the tests to verify if everything is working correctly

<img src="img/2 InMemoryPersistenceTest.png">

> 3. We made a program in which you create (using Spring) an instance of BlueprintServices, and rectify its functionality: register plans, query plans, register specific plans, etc.

**Testing the program**

<img src="img/3 Ejecución.png">

> 4. The query operations are expected to perform a filtering process before returning the queried plans. These filters aim to reduce the size of the plans by removing redundant data or simply subsampling them before returning them. Adjust the application (adding the abstractions and implementations you consider) so that one of two possible "filters" (or possible future filters) is injected into the BlueprintServices class. The use of more than one at a time is not contemplated:
* (A) Redundancy filtering: removes consecutive points that are repeated from the plan.
* (B) Subsampling filtering: removes one out of every two points from the plan, in an interleaved manner.

We created a *BlueprintFilter.java* interface, and then the two implementations: (A) *RedundancyFilter.java* and (B) *SubsamplingFilter.java*

<img src="img/4 BlueprintFilter.PNG">

Then, the filter is injected to *BlueprintsServices.java* through *@Autowired* annotation and make the call on the *getBlueprint* and *getBlueprintsByAuthor* methods.

<img src="img/4 BlueprintsServices.PNG">

> 5. Finally, we had to add the corresponding tests to each of these filters, and test their operation in the test program, checking that by only changing the position of the annotations - without changing anything else - the program returns the filtered plans in the manner (A) or in the manner (B).

We created the tests and execute them

<img src="img/5 filtersTests.png"> <img src="img/5 executeTests.png">

Using this points to test the filters:
```java
Point[] pts3 = { new Point(0, 0), new Point(0, 0), new Point(0, 0), new Point(10, 10), new Point(20, 20) };
```

After executing with *RedundancyFilter* it's possible to see that there's only one instance of Point (0,0)

<img src="img/5 redundancyFilter.PNG">

Then, we changed the *@Qualifier* annotation in *BlueprintsServices.java* to use *subsamplingFilter* instead and the results were correct

<img src="img/5 SubsamplingFilter.png">