# Groovy
- Built on Java so gets compiled into Java Bytecode and gets run on JVM
	- interacts seamlessly with Java classes and libraries
	- can call Java code from Groovy and vice-versa
- often used as a Domain Syntax Language (DSL)
- Groovy shell allows you to execute groovy code interactively
- compiled-language with scripting capabilities
- Dynamically-typed so don't need to explicitly declare variable types
## Scripting
- scripting engine provides scripting capabilities so you don't need a formal compile step (does it behind the scenes)
## Syntax
- has syntactic sugar for methods (no parenthesis), among other differences 
### Closures
- self-contained blocks of code that can be treated like variables
- can pass them to functions, store them, and execute them later
### Builders
- provide a clean, declarative syntax for creating hierarchical objects like XML, JSON, or other tree-like data
- reduces boilerplate code compared to manual object creation
- how Jenkins uses a groovy-based DSL
### Metaprogramming
- allows you to change the structure or behavior of classes, methods, and properties at runtime