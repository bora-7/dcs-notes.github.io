# CS118 Notes - Edmund Goodman

[toc]

## Introduction to programming

 - Ways to classify programming languages
   	- High and low level languages
      - Low level languages only support simple operations, which they can run very quickly, but are difficult to program complex tasks in due to this restriction
      - High level languages rely on abstractions on top of low level languages to perform more complex operations, but these operations run slower as they are composed from many smaller ones, and are often less fit for purpose, as they are generic for many use cases
    - Programming paradigms
      - Imperative, instructing a machine how to change its state
        - Procedural - Grouping sequences of instructions into procedures
        - Object orientated - Grouping data and operations on the data together to model systems
      - Declarative, declaring the properties of the desired result, but not how to compute it
        - Functional - Declaring the result as the value of a sequence of function applications
        - Logical - Declaring the result as the answer to a question composed of a set of axioms and inference rules
- Java is an object orientated programming language
  - Provides automatic memory management, called garbage collection
  - Error checks at both compile and runtime
  - Doesn't compile straight to machine code like most compiled languages, but instead to "bytecode", which is not human-readable, but is portable between machines, unlike machine code, which is specific to an individual machines architecture/instruction set
    - The java code is first compiled to bytecode (Using the `javac` command, producing a `.class` file)
    - The bytecode in this generated file is then interpreted into machine code in the JVM (Java virtual machine), using a JIT (just in time) compiler, so the computer can execute it (Using the `java` command)
- The process of converting the specification of what a program should do to program code is known as "refinement". This often includes five main things
  - Precondition - when should the program work
  - Input - what data does the program need
  - Calculates - what should the program calculate
  - Output - what results does the program return
  - Postcondition - what is the state after execution

## Variables, operators and types

- Variables store values, associating them with a name so they can be used symbolically
  - Variables require a type and a name, given by `[type] name;`, defined in a process called instantiation
  - They can then be assigned a value, either on the same line or after instantiation `name = value`
- Number systems
  - Numbers are stored in binary
  - In order to store negative integer numbers, we don't just add a "sign bit" as we would normally, as then we define both positive and negative values for zero, which wastes space, and can be confusing. Instead, we use "Two's complement"
    - Binary representation, but the left-most bit is negative
      ![image-20210407140738981](C:\Users\egood\AppData\Roaming\Typora\typora-user-images\image-20210407140738981.png)
    - With eight bits, the smallest value is $10000000_2 = -128_{10}$, and the largest value is $01111111_2 = 127_{10}$ (this is $-2^n $ to $2^n - 1$)
  - In order to store decimal numbers, we use "floating point notation", which automatically handles the trade-off between precision and range for storing numbers in a fixed number of bits. This is defined in the IEEE-754 specification (for single precision `floats`, `double`s are a bit different):
    ![image-20210407142331103](C:\Users\egood\AppData\Roaming\Typora\typora-user-images\image-20210407142331103.png)
    - The "sign bit" denotes whether the number is positive or negative
    - The "exponent" needs to represent both positive and negative numbers, but it **does not** use two's complement, it instead uses a bias of $127$, meaning you subtract $-127$ from the decimal value to get the exponent. Additionally, $-127_{10} = 00000000_2$ and $128+{10} = 11111111_2$ are reserved special cases. Double precision uses an 11-bit field instead of an 8-bit one, and hence has an offset of $1023$
    - The "fraction/mantissa" represents the precision bits of the number, with the most significant bit being $\frac{1}{2^1}$, then $\frac{1}{2^2}$, and so on. This is taken as the value after the binary point, and in almost all cases, the value before the binary point is one, however, if the exponent is all $0$s, it is $0$ and said to be denormalised.
      As a rule of thumb, calculate the value of the fraction from binary with the reciprocal values, the add one to it
    - The fraction represents the value of the number, and is of fixed precision, for example, 23 bits of information. The exponent then specifies the range of the number.
    - Special values
      - Denormalised, when the exponent is all $0$s, so the number is assumed to have a leading $0$ not a leading $1$ before the binary point, i.e. $0.[fraction]$ not $ 1.[fraction]$
      - Zero, when both the exponent and fraction are all $0$s, so it resolves to $0.0$ . Due to the sign bit, there are both positive and negative zeroes, but they compare as equal
      - Infinity, positive and negative infinities are when the exponent is all $1$s, but the fraction is all $0$s, and the sign bit indicates sign. Since they are actual values in the scheme, we can do operations on them
      - NaN (not a number), when the exponent is all $1$s, and there is a non-zero fraction. These can be used for signalling errors etc.
  - A tool for **checking** this is available: https://www.h-schmidt.net/FloatConverter/IEEE754.html, and additional resources are available: https://steve.hollasch.net/cgindex/coding/ieeefloat.html
  - An example of this in practice is:
    ![image-20210407142354801](C:\Users\egood\AppData\Roaming\Typora\typora-user-images\image-20210407142354801.png)
    Note that the "1.(fraction)" denotes a decimal point, not a multiplication operation
- The data types encoding have limited range, with the Java primitives having the ranges:
  - `byte`, $-128$ to $127$ (1 byte)
  - `short`, $-32768$ to $+32767$ (2 bytes)
  - `int`, $-2,147,483,648$ to $+2,147,483,647$ (4 bytes)
  - `long`, $-2^{63}$ to $2^{63}-1$ (8 bytes)
  - `float`, -3.4e38 and 3.4e38 with 6 to 7 significant digits of accuracy (32 bits)
  - `double`, -1.7e308 and 1.7e308 with 14 to 15 significant digits of accuracy (64 bits)
- Java primitives are data types where the data value is stored directly in memory, as opposed to a reference to it being stored (as is done for library or user defined objects). There only are eight java primitives `byte`, `short`, `int`, `long`, `char`, `boolean`, `float` and `double`. Primitives have default values they take when initialised, but not set to a value, for most numeric types, this is $0$.
- Precision and casting
  - Sometimes, we may want to change the precision the data type storing a value can support. We do this in a process called (type) casting, of which there are two types
    - Explicit/narrowing casting is when the value is moved to a data type which supports less precision. Data can (possibly) be lost in this operation. This most be indicated with a forced cast, which brackets to show it, e.g. `float wideValue = 3.141f; int narrowedValue = (int) wideValue`
    - Implicit/Widening casting is when the value is moved to a data type which supports more precision. No data is lost in this operation. This can be done simply by setting a more precise equal to  the value of a less precise one, e.g. `float narrowValue = 3.141f; double widenedValue = narrrowValue`
- The `char` primitive is essentially an unsigned `short` primitive, which is used to store UTF-16 encoded single characters
- The `boolean` primitive can take only two values, `true` and `false`, and is returned by boolean operators, including comparisons
- Types of operators
  - Comparison operators: `==, !=, >=, <=, <, >`
  - Boolean operators: `&, &&, |, ||, !`
- Lazy and strict operators
  - Lazy operators aren't executed if an earlier operations fully defines the statement, e.g. the value is an or, and the first statement is true, the second statement needn't be tested
  - Strict operators execute everything, even if it isn't "needed" to evaluate the statement, as state might be mutated in later parts, e.g. `y && (x++ == 4)` would change the value of `x`
- Initialising variables allocates space in memory. Variables need to be initialised along all code paths, as otherwise the memory might not be allocated
- Pre-increment and post-increment
  - `++i` increments the variable before it is used in the equation,`i++` increments it after it has been used.
- Operator precedence
  - Postfix (`expr++`, `expr--`)
  - Unary (`++expr`, `--expr`)
  - Multiplicative  (`*`, `/`, `&`)
  - Additive (`+`, `-`)
  - Shift (`>>`,`<<`)
  - Relational (`>`,`<`,`<=`,`>=`)
  - Equality (`==`,`!=`)
  - Strict operators (`&`,`|`,`^`)
  - Lazy operators (`&&`,`||`)
  - Assignment (`=`,`+=`,`-=`,`*=`,...)

## Conditionals

- The `if..else if..else` statement

  - Boolean values are used to decide whether code blocks are run

    - ```java
      if (condition_1) {
          //If condition_1 is true, execute the exit the whole statement
      } else if (condition_2) {
          //If condition_1 is false, and condition_2 is true then execute the exit the whole statement
      } else if (condition_3) {
          //If condition_1 is false, condition_2 is false, and condition_3 is true then execute the exit the whole statement
      } else {
          //If none of the preceeding conditions are true, execute
      }
      ```

  - The curly braces can be omitted for single line statements, however, this is less readable, and can lead to issues such as the dangling else, so in my opinion should be avoided

- Ternary operators are a shorthand for assigning values using `if..else` statements

  - They evaluate as follows `output = (condition) ? (valueIfTrue) : (valueIfFalse)`
  - They can be nested, but this is code smell, and should probably be reverted to an `if..else if..else` statement instead

- The `switch` statement

  - `Switch` statements offer a way to select control flow based off the value of a variable as opposed to a boolean condition. In some cases, this can be more simple, and hence preferred over `if` statements

  - ```java
    switch (variable) {
            case value_1:
                //If the variable is equal to value_1, execute the code, then exit the statement due to the break statement
                break;
            case value_2:
                //If the variable is not equal to value_1, and is equal to value_2 then execute the code, and continue executing each block until the next break statement occurs, or the statement ends
            case value_3:
                //If the variable is not equal to value_1, and is equal to either value_2 (since it is running through with no break statement) or value_3, then execute the code, then exit the statement due to the break statement
            	break;
            default:
            	//If no cases have been satisfied, or the code runs through due to no break statements, execute the code
    }
    ```

  - Both the `break` and `default` sections are optional

  - Objects should not be switched on, only primitives, as the equality will compare their memory reference with the `==` operator, rather than calling the `.equals()` function.

- We can reason carefully about what happens in conditionals by writing out the preconditions (similar idea to the explanation above, but easier to do it in boolean expressions to be terse)

## Iteratives

- Bounded repetition

  - Bounded repetition is when the number of times to loop is known in advance

  - We know "where we wish to start, where we wish to end, and what iterative step to take each repetition"

  - The `for` statement implements bounded repetition

    ```Java
    //for (initialisation; booleanExpression; iteration) {}
    
    //Each iteration, increment a counter from 0 by 1 until it is greater than five, i.e. repeat six times
    for (int i=0; i<=5; i++) {
        //Execute some code
    }
    ```

- Unbounded repetition

  - Unbounded repetition is when the number of times to loop is not known in advance, merely the condition to stop looping. The can lead to infinite loops
  - The `while` statement implements "pre-test" unbounded repetition
    - A boolean expression is checked, then a code block is repeatedly run if it is true, and it moves on if it is false
    - The expression is checked **before** the code is run
  - The `do..while` statement implements "post-test" unbounded repetition
    - A code block is run, then a boolean expression is checked, then the code block is repeatedly run if it is true, and it moves on if it is false
    - The expression is checked **after** the code is run
  - Both statements are equivalent, as external boolean statements can be used to make the first test on the `while` loop always true

- The `break` and `continue` keywords

  - `break` causes immediate exit from the highest level loop (current scope), moving on to the code after it
  - `continue` skips the current iteration of the highest level loop (current scope), going back to the start of the next iteration at the beginning of the statement

## Arrays and Methods

- Arrays

  - Store "lists of monomorphic data" in a neat (single variable name) and efficient (indexable items) way
  - Created with the syntax `[type][] name;` e.g. `int[] arrayOfInts`
  - Arrays can be multidimensional, with each item itself being an array, but all items must be of the same type throughout. E.g. `int[][] matrix`
  - When the array is initialised, it starts at the special value null, and Java doesn't allocate any memory to it, since it doesn't know its size
  - Arrays can be instantiated using the `new` keyword, which reserves a block of contiguous memory. The size of the array must be provided at this point, so Java can calculate how much memory should be allocated given the number of items and the size of each item from its data type. An example of this is `int[] arrayOfInts = new int[5] `
  - The items in arrays can be "indexed", looking up values at a cell location. Indexing starts at 0 for the first cell, and the syntax is `array[index]`. Values can be got and set this way
  - Arrays can also be defined inline using the shorthand syntax `int[] arrayOfInts = {1,2,3,4}` 
  - Arrays have only one property, which is `array.length`. This tells us the number of indexable cells the array contains

- Methods

  - The main method is a special method which is the "entry point" for java programs

  - All methods have "signatures", which describe their name, return type, access modifiers, and parameters

    - Example: `public static void main(String[] args) {  }`

      [Access modifier] [keyword (optional)] [return type] [name] ([arguments..]) {  }

  - The `return` statement

    - Tells the method which value to return. This value must be of the type indicated in the method signature

  - Methods can call themselves in a process called recursion (See the cult of CS141...)

  - Method overloading

    - Two methods can have the same name, as long as they have different parameters. Which method will be run will be selected by which one's signature fits the type and number of parameters passed

  - Scope

    - Variables have scope, which is defined as the block and all child blocks in which it is initialised
    - When a function is called a new "computation environment" is created, and all variables declared outside the function do not have scope within it
    - Variables must have a unique name within a scope
    - Sometimes we want to be able to access variables across many methods. We can define class variables to do this, which are variables which have scope across the entire object. These are defined at the start of the class, not within a method, before the constructor

- Passing by value and by reference

  - We said that primitive variables directly store their values in memory. When they are provided as parameters, they are "passed by value". This means that the value of the variable is cloned in memory to a new variable in the new scope. If the variable is changed in that new scope, it is unaffected outside of it, where it was passed from
  - Non-primitive variables (e.g. objects and arrays) are not store in memory. When they are provided as parameters, they are "passed by reference". This means that if they are mutated in the new scope, they will also change in the old one, since both references point to the same place, and the item is being changed in that place

## Objects

- Paradigm shift from procedural/imperative to object orientated programming
  - Procedural defines a sequence of steps to achieve a goal
  - Object orientated models systems as data (properties) and operations on the data (behaviour) in a structure called an object
    - The operations on the data are procedural, so object orientated encompasses procedural
    - Essentially just a way of organising code to bundle data and operations together, providing a convenient abstraction
    - Object orientated programs can be considered as a collection of co-operating objects
- Classes are the "blueprint" for objects, defining what data will be stored, and the implementation of the algorithms on it, whereas objects are "instances" of classes, containing the data, and allowing the operations to be performed on it
- The `class` keyword defines a new class, and `public` classes need their own file (by convention, classes start with a capital letter)
- Constructor methods are special methods that have the same name as the class and return no value. They "set up" the object, normally assigning values to variables. (Objects needn't have constructors, they are merely overriding the default constructor, which performs no operations)
- New instances of an object are created using the `new` keyword, which allocates space in memory and calls the constructor. If an object is initialised but not instantiated, it takes the special `null` value as with arrays
- The `this` keyword refers to class variables. It can be omitted normally, unless there is a name clash with a local variable of the same name.
- Objects are created from classes using the `new` keyword. This reserves space in memory for the default values of the object, and calls the constructor
- To compare objects, we need to use the `.equals()` method, which compares the state of the all variables within the object (and can be overridden), as opposed to the `==` operator, which compares the actual memory location, so would only return true for two references to the same object instance, not just two objects with the same values

## Pillars of object orientated programming (Additional)

There is some argument over the number of "pillars", but the four commonly accepted ones are:

- Abstraction
  - "Exposing essential features, whilst hiding irrelevant detail" 
  - Used so that the purpose/interface of a function can be separated from its implementation, making code re-usable, more understandable, and easier to modify (won't break things, changes need to be made in fewer places)
- Encapsulation
  - "Bundling data and operations that can be performed on that data together - leads to data hiding"
  - "the action of enclosing something in or as if in a capsule", so that an object controls its own internal state, and should only be able to be changed through its interface. This is often implemented through/results in data hiding, which is removing access to variables within a class by making the `private`, and then providing getters and setters, rather than direct access to the data.
- Inheritance
  - "Creating new classes from existing ones, reducing programmer effort"
  - "Allowing an object to acquire the properties and methods of another object", so that code can be re-used, along with helping abstractions and hierarchy.
- Polymorphism (definition "the condition of occurring in several different forms")
  - Using objects that can take many forms - allows us to invoke operations from derived classes while using a base class reference at run-time

## Modifiers

- Access modifiers, e.g. `public` and `private`

  - `public` means something that can be accessed outside the class, `private` something that can't be (`protected` means something that can be accessed by something in the same package or subclasses)
  - These restrict the access to an objects properties an behaviours, meaning we can more closely control the interface provided for the object
  - Which modifier should be picked with common sense, and thinking about what interface we're trying to provide
  - The constructor clearly must be `public`, as we need to access it from outside the object, as the object doesn't exist yet
  - No access modifier defaults to "package private", which is somewhere between `private` and `protected` (can only be accessed by objects in the same package)

- Encapsulation and data hiding

  - Usually all state variables are `private`, but we might need a way to change the externally
  - Making state variables `private` and the methods that act on them `public` is called data hiding
  - Encapsulation is "wrapping data and the code acting on it together as a single unit", so users must use the external interface, rather than just changing things internally.
  - Encapsulation is not data hiding, but it does lead to it
  - This has the beneficial property of the internal implementation being hidden, so it can be changed and improved without changing the external interface, so other code doesn't break. Additionally, it hides the unnecessary detail of the implementation, and clarifies responsibility for the code
  - Data hiding is often implemented using "getters" and "setters", which are methods which allow internal private variables to be changed. This means that some internal variables can not be externally changed if no method is provided, and operations such as validation can be performed, instead of just directly changing the data

- Class/instance/final variables

  - Instance variables have values attached to a specific instance of a class. This is the default for variables defined in classes
  - Class variables allow data to be shared between instances of a class. This is denoted by the `static` keyword, and starts to act more like imperative programming.
  - These static variables/methods can be called/interacted with anywhere through the name of the class, but a static method cannot call a non-static method

  - `final` variables define a constant whose value cannot be changed during the program

- Enumerated types

  - A special type of class that hold a range of constant values

## Inheritance

- Inheritance is properties that a taken from a parent
  - We call the "parent" a base of a super class
  - We call the "child" a derived or sub class
  - A class is indicated as inheriting from a superclass using the `extends` keyword, e.g. `public class Lion extends Cat`. Classes can only inherit from one superclass in Java (but sometimes more in other languages)
  - A subclass inherits all the properties from its superclass, and may have some additional ones of its own, and/or override some of the parents, however, it must not remove methods from the parent class (known as Liskov substitution principle - not examined)
  - Overriding is when a subclass changes the definition of a function defined by its superclass. It is somewhat like overloading with different numbers of parameters for methods. If a method defined in the superclass is re-defined for the subclass, it will override it. This means that instances of the superclass will run the superclass definition, but those of the subclass will run the subclass definition, without the parent class being modified, and the subclass still retaining all other parent properties without having to redefine them
  - The `super` keyword is like the `this` keyword for the superclass. We can then use `super()` to call the superclass constructor in the subclass, so it doesn't need to be redefined (it must be the first thing that happens in the subclass constructor)
  - `protected` properties cannot be "seen" externally, but can be "seen" by subclasses, meaning we needn't go through or define accessor methods for such properties
  - All classes are ultimately subclasses of the `Object` class
- Polymorphism
  - "Polymorphism describes its ability to process objects of different types through a single uniform interface"
  - "Inheritance lets us  inherit attributes and methods from another class. Polymorphism  uses those methods to perform different tasks. This allows us to perform a single  action in different ways." (https://www.w3schools.com/java/java_polymorphism.asp)
  - We can pass `Object` classes into any method that takes any non-primitive type, since everything is a subclass of it
  - Static vs dynamic polymorphism
    - Static polymorphism occurs at compile time, for example method overloading an overriding, as different behaviour is exhibited by the same method dependent on the input types
    - Dynamic polymorphism occurs at runtime, and allows us to cast objects which are stored in their superclass to their subclass, so we can use their subclass interfaces
  - The `instanceof` keyword tells us whether an object is of a given type of class. We can use this to check when we need to cast objects so we can use their appropriate interfaces

## Abstract classes and interfaces

- In object orientated programming, each subclass is more specific than its superclass, and each superclass more general than its subclass. At some point, superclasses become so general they become what we call "abstract"
  - Abstract classes are a special type of class which allow us to capture common properties and behaviours at an abstract level. The `abstract` keyword is added to the class signature denotes them, e.g. `public abstract class Feline`
  - They cannot be initialised, and so don't need a constructor method
  - They can contain a mix of abstract and concrete methods (again, `abstract` keyword added to method signature)
    - Abstract methods are ones which must be provided by all subclasses, but will be different for different subclasses, so no implementation is given
    - Concrete methods are ones which will be the same for all subclasses, so they are fully defined, and will just be inherited as usual into the subclasses
  - Concrete (i.e. default/normal) classes can then inherit from abstract classes
    - We can omit any concrete definitions/variable instantiations which are inherited, but **must** provide implementations for all abstract functions
  - They needn't implement all of the methods a subclass will provide
- Eventually, we end up in a situation where abstract classes contain only abstract methods. This is then called an "interface". This time, the `interface` keyword is used instead of the `abstract` one
  - Interfaces can only contain methods without implementations, but these these methods shouldn't be declared `abstract` nor `interface` merely the method signature provided.
  - Whilst only one class can be inherited, multiple interfaces can be "implemented". This is to avoid the issue of two superclasses providing different implementations for the same method
  - The `implements` keyword is used instead of the `extends` one to indicate inheritance/implementation
- Typically, abstract classes are used to "provide partial implementation", and interfaces to "encapsulate a small subset of functionality"

## Exceptions

- The `try..catch..finally` statement
  - The code in the `try` block is executed. If an exception is generated, the appropriate `catch` block is executed
  - Multiple `catch` blocks can be used, but they act like a `switch` statement, moving downwards, selecting the first to match, so if a subclass lies below a superclass, it will never be executed
  - The `finally` block is run irrespective of whether the `catch` was executed, even if there is a `return` within one of the blocks
- The `throws` keyword
  - Can be added to a method declaration to indicate that it might "throw" a checked exception of a given type
- Exceptions
  - In Java all errors and exceptions are subclasses of the Throwable class
    - Errors are " serious problem that a reasonable application should not try to catch". These are rare, so we will largely ignore them
    - Exceptions are "conditions that a reasonable application might want to catch"
  - There are two types of exceptions
    - Checked exceptions, which must be caught or re-thrown for the code to compile
    - Unchecked exceptions are subclasses of either Error or RuntimeException. They do not need to be specifically thrown, and while they can be caught, they don't need to be for compilation
  - Custom exceptions can be written
    - The exception should inherit from the most specific exception class that encapsulates it
    - Normally, they are just defined as inheriting from the superclass exception, then overriding the 4 polymorphic constructors to provide appropriate error messages etc.
- The `throw` keyword (not the same as `throws`)
  - Used to throw a new exception from a method
  - The throw keyword requires that it is followed by an object that is an instance of the Throwable class (e.g. `throw new RuntimeException`)

## Generics

- Generics allow code to enforce strong type checking at compile time, which helps avoid errors such as invalid casting at runtime. Additionally, they minimise the number of type casts needed, as they automatically return data of the specified type, and they allow writing generic algorithms to be tailored to different types
- Angle brackets are used to indicate the variables that will be used, e.g. `new MyClass<String>`
- Type placeholders are used in the class definition to receive which class the generic is instantiated with, e.g. `public class MyClass<V>`
  - The naming convention for the placeholders is a single uppercase letter, often of the start of what it refers to, e.g. E from element, or V from value
  - The type placeholder is then used instead of a concrete type e.g. `Object` throughout the class
- Primitives cannot be provided as generics, as they are not objects. Instead, we need to use their object wrapper (e.g. `int` becomes `Integer`). Then, if an integer is passed in as a parameter, it will be "autoboxed" into its object wrapper to be handled internally.
- Types of generics can be restricted by using the extends keyword in the instantiation, e.g. `public class MyClass<V extends Number>`
