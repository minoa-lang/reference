# Implicit Context

Minoa passes an implicit context to all function and method calls and can be access in any one of them (assuming it uses the 'Minoa' ABI).

The context is passed to all function implicitly, and can be accessed from any valid locations.
All data in the context is immutable and can only be written to via [interior mutability].

Each member of the implicit context is stored within an optional, which by default will have a value of `.None`, and must be explicitly initialized by the program.
Since it's not possible to determine the exact order used to drop member (as libraries can add their own members), each member needs to be explicitly dropped by calling the explicit `.drop()` method on the member

The implicit context can be accessed via the `#context` meta-variable.

## 18.1 Defining context [↵](#implicit-context)

Each libary is allowed to define any amount of additional context members, but they need to have unique names.

Context member can be defined in 2 ways:
- as a fixed type member
- as a trait member

A trait member can be defined from outside of the library adding it, a fixed type needs to be done via the library defining it.

## 18.2. Internals [↵](#implicit-context)

The context is passed via a pointer in a fixed register, the context itself contains a number of nullable pointers to each individual member.
Members are accessed via a property.

Libraries define an external symbol, which is the index into the pointer array, while binaries define the final layout inside of the context and define the required symbols to access member correctly.



[interior mutability]: ./type-system/interior-mutability.md