# Eb Language


## Introduction

Eb (Also E&#9837; or E-Flat) concretely has the following goals:

* Extensive support for metaprogramming:
  * Support for compile-time execution of arbitrary parts of a program;
  * Metaprogramming constructs leverage the full power of the Eb language (avoiding [Shadow Worlds]);
  * Support for most features as first-class constructs, subject to the full power of the Eb language.
* Encourage a functional, declarative programming style over a procedural one.


## Name

Eb may be pronounced as "E-Flat" or like the word "ebb".

The "Flat" in E-Flat comes from its aim to broadly support its features as first-class, which could be said to be a "flat" design since it lacks a hierarchy that makes fundamental language features incompatible. Additionally, its aim to encourage a functional and declarative programming style can be said to create a "flat" source code layout, without any hierarchy or order enforced at the language level.

The "E" in E-Flat is a reference to the language's inspirations. Its strongest inspiration is [JavaScript], which conforms to the [EcmaScript], where it borrows the "E". The "E" does not *stand* for Ecma as Eb is not affiliated with [Ecma International] in any way; it is simply a way to pay respect to its inspirations.

It bears mentioning that there is also the [E programming language][E], to which Eb is not directly related.


## Features

* [Metaclasses]


## File Extensions

Eb source files should use the file extension `.eb`.


## Disclaimer

Any syntax shown in Eb exaples is not final at this time.


<!-- Internal Links -->
[Metaclasses]: Features/Metaclasses.md
[Shadow Worlds]: Concepts/ShadowWorlds.md

<!-- External Links -->
[E]: https://en.wikipedia.org/wiki/E_(programming_language)
[Ecma International]: https://en.wikipedia.org/wiki/Ecma_International
[EcmaScript]: https://en.wikipedia.org/wiki/ECMAScript
[JavaScript]: https://en.wikipedia.org/wiki/JavaScript