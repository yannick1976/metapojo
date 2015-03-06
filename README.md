# meta-pojos
The purpose of meta-pojos is to provide a very intuitive and easy-to-use api to browse your Java code.
The querying is done in Java, allowing you to use your favorite IDE for content assist.
The results are output to console. If you use eclipse the output will be hyperlinks to places in your code.
## Setup
Once you have downloaded the project, import meta-pojos-main into your IDE. It is a maven project.  
I don't know if you'll find interest in the other project, meta-pojos-test-classes : is a just a sample project to browse. Browsing your own projects should be more interesting !
```
meta-pojos
|-meta-pojos-main
|-meta-pojos-test-classes
```
Then, have a look at the class meta-pojos-main/src/test/java/com/yannicklerestif/metapojos/MetaPojosTest.
It should work as is, you only need to change the directory where you put meta-pojos.

## Launching
You init MetaPojos like this :
```java
MetaPojos mp = MetaPojos.start();
mp.readClasses("/home/yannick/Projets/meta-pojos/meta-pojos-test-classes/bin");
```
you can use class directories, jars, or both :
```
mp.readClasses("myDir1", "myJar1.jar", "myDir2");
```
Use only one `readClasses` instruction.  
Classes directories must be in **binary** format (.class files, not source files).

## Querying
A query will then look like this :
```java
mp.singleClass("test.model.hierarchy.MyInterface").getMethods().getCallsTo().print();
```
There are two starting points :
- `mp.singleClass(String className)` (or add your project as a dependency and use `com.yourcompany.YourClass.class.getName()`)
- `mp.allClasses()`

From here, you can just use content assist to see what you can do, or read ahead for some more explanations :

### How does it work
The two methods above return a `ClassStream` object, that wraps a Java 8 `Stream<ClassBean>`, `ClassBean` being 
the class that represents a Java class.  
On `ClassStream` you can for example apply `getMethods()`. This will return a `MethodStream`, wrapping a stream of all the methods in the classes in the stream.  
Then, from the methods stream, you can get all the calls from these methods, or all the calls **to** these methods... and so on.  
But because object streams wrap streams, you can also use some stream methods, like `filter`, `foreach`, and do whatever you like.

### The output
Output will look like the following. On eclipse hyperlinks are clickable.

Call from test.model.StartingClass.startingMethod([StartingClass.java:9]()) to test.model.hierarchy.MyInterfaceImplChild.\<init>([MyInterfaceImplChild.java:3]())  
Call from test.model.StartingClass.startingMethod([StartingClass.java:10]()) to test.model.hierarchy.MyInterface.myInterfaceMethod([MyInterface.java:-1]())   
Call from test.model.StartingClass.\<init>([StartingClass.java:6]()) to java.lang.Object.\<init>([Object.java:-1]())  
Call from test.model.hierarchy.MyInterfaceImplParent.\<init>([MyInterfaceImplParent.java:3]()) to java.lang.Object.\<init>([Object.java:-1]())  
Call from test.model.SomeClass.doSomething([SomeClass.java:5]()) to java.lang.Object.\<init>([Object.java:-1]())  
Call from test.model.SomeClass.doSomething([SomeClass.java:6]()) to java.lang.Object.equals([Object.java:-1]())  
Call from test.model.SomeClass.\<init>([SomeClass.java:3]()) to java.lang.Object.\<init>([Object.java:-1]())  
Call from test.model.hierarchy.MyInterfaceImpl.\<init>([MyInterfaceImpl.java:3]()) to test.model.hierarchy.MyInterfaceImplParent.\<init>([MyInterfaceImplParent.java:3]())  