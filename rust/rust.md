# Rust
## about
- rust is designed to use ahead-of-time compilation:
  - translation of program to machine code is completed before it begins execution
-  all Rust functions are thread-safe
  - if program compiles, it is free of data races
- only  ASCII characters may appear in byte literals
  - since ASCII code for A is 65, literals b'A' and 65u8 are exactly equivalent
- vectors and hash maps store their data on heap
- difference between shared and mutable references
  - to enforce multiple readers or single writer rule at compile time
  - as long as there are shared references to a value, not even its owner can modify it
    - the value is locked down
  -  whether two references point to the same memory:
    - use `std::ptr::eq`, which compares them as addresses:
     ``` rust
       assert!(rx == ry);         // their referents are equal
       assert!(!std::ptr::eq(rx, ry)); // but occupy different addresses
     ```
- rust doesnot have exceptions
- rust doesnot use the term object much, preferring to call everything a value
- `?` can only be used in functions that have a `Result` return type
- Only calls through `&mut Write` incur overhead of a virtual method call (Traits)
- str are compared by their byte values
- `{...}` forms are called *format parameters*
  - have the form {which:how}, both parts are optional
- `{}` is Display format specifier, works for things that has Display trait
- `{:?}` is Debug format specifier works for things that has Debug trait
- `{:#?}` for pretty-print
- `{{` or `}}` use it to print `{}`
- `=` stores the value of rhs expression in the "place" named by lhs
- when you see expected type `()` in error msg, look for missing semicolon first
- `Vec` and `HashMap` do not implement `Display` as they lacks human readable form
- all blocks of `if` and `match` expression must produce values of same type
- blocks are also expressions, mean they evaluate to a value
- `::` operates on namespace
- `..` can be only used once per tuple pattern
- `::<...>` is called turbofish
- `use` directives can be used to "bring in scope" names from other namespaces
  - in use directives, curly brackets are "globs"
- Using @ lets us test a value and save it in a variable within one pattern
- panic: abrupt termination
  - not a crash
  - not undefined behavior
  - more like a RuntimeException in java
- `assert!` panics with a helpful message and source location of the failing check
- methods can be called as regular functions
- rust inserts this at the beginning of every module:
``` rust
use std::prelude::v1::*;
```
- `&mut` means exclusive access
- `&` means shared access

## memory model
- when value is assigned to a variable, that value is from then on named by that variable
- when a variable is later accessed
  - imagine drawing a line from the previous access to the new access (called 'flows')
    - which establishes a dependency relationship between the two accesses
    - you cannot draw lines from variable whose value is uninitialized or has been moved
- a variable exists only so long as it holds a legal value
- pushing values on stack is not considered allocating

## array
- array indices are usize values
- array is `'static`
- instead use `Iterator::map` by calling `.iter()` or `.into_iter()` on array as
  - [T; N]::map is only necessary if needed a new array of same size in result
  - lazy iterators tend to get optimized very well

## struct
``` rust
// This is called "struct update syntax",
//can only happen in last position, and cannot be followed by a comma
let s2 = struct2 {
    x: 14.0,
    ..struct2
};
```

## crates and modules
- crates are about code sharing between projects
- modules are about code organization within a project
- rust never compiles its modules separately, even if they are in separate files:
  - when a rust crate is built, all of its modules are recompiled
- modules are namespaces, they’re containers for:
    - functions, types, constants, and so on that make up program or library
- `super` is an alias for the parent module
- `self` is an alias for the current module
- while paths in imports are treated as absolute paths by default
  - `self` and `super` let you override that and import from relative paths
- submodules can access private items in their parent modules
  - but they have to import each one by name
  - `use super::*;` only imports items that are marked pub

## TODO
- methods are also known as **associated functions**,
  - since they’re associated with a specific type
- opposite of an associated function is a **free function**
  - one that is not defined as an impl block’s item
- ref is checked at compile time while RefCell at runtime(panic)
- the trait itself must be in scope, otherwise, all its methods are hidden
- use statics for larger amounts of data, or borrow a reference to the constant value
- statics can be marked mut but that is not thread-safe
- `use` and `extern` crate declarations are items too
  - even though they are just aliases, they can be pub
- to attach an attribute to a whole crate:
  - add it at the top of main.rs or lib.rs file,
    - before any items,
    - write #! instead of #
- `assert_eq!(v1, v2)` is just like `assert!(v1 == v2)`
  - except that if the assertion fails, the error message shows both values
- `debug_assert!()` and `debug_assert_eq!()`  are checked only in debug builds
- to test error cases, add the `#[should_panic]` attribute
- start test names with “should”
- `String` uses a `Vec<u8>` internally to hold its text
  - so String need not implement Drop itself
  - it lets its Vec take care of freeing the characters
- if a type implements `Drop`, it cannot implement `Copy` trait,
- by default, struct and enum types are not `Copy`,
  - can implement `Copy` on them also
- enums are `Sized`
  - whichever variant is present, enum occupies enough space to hold its largest variant
- a `Vec<T>` owns a heap-allocated buffer whose size can vary
  - the Vec value itself is a pointer to the buffer
  - it has capacity and length, so Vec<T> is a sized type
- only implement `Into` if conversion to a type outside the current crate is required
  - `From` cannot do these type of conversions because of rust's orphaning rules
- prefer using `Into` over `From` when specifying trait bounds on a generic function
  - this way, types that directly implement `Into` can be used as arguments as well
- As a stopgap, trait implementations are statically generated up to size 32
- `From` trait is also very useful when performing error handling
- `From<T>` for U implies `Into<U>` for T
- `From` is reflexive, which means that `From<T>` for T is implemented
- given `From` implementation, std lib automatically implements corresponding `Into`
- fn value is memory address of the function’s machine code,
  - just like a function pointer in c++
- `parse()` can parse any type that implements the `FromStr` trait

## borrowed type
-types provide access to the underlying data through references to the type of that data
 - they are said to be **borrowed as** that type
 - eg: a Box<T> can be borrowed as T while a String can be borrowed as str
- types express that they can be borrowed as some type T by implementing `Borrow<T>`
  - providing a reference to a T in the trait’s borrow method
  - a type is free to borrow as several different types

## ownership
- all values have single owner
  - enforced through borrow checker
- references are never null
- borrowing rule:
  - can have either (not both) one mutable reference or any number of immutable references
- references must always be valid
- no lifetime annotation required when `self` is a parameter
- throughout its lifetime, a shared reference makes its referent read-only
  - may not assign to the referent or move its value elsewhere
- shared access is read-only access
  - values borrowed by shared references are read-only
  - across the lifetime of a shared reference:
    - neither its referent, nor anything reachable from that referent, can be changed
  - there exist no live mutable references to anything in that structure:
    - its owner is held read-only and so on
    - it’s really frozen
- mutable access is exclusive access
  - value borrowed by a mutable reference is reachable exclusively via that reference
  - across the lifetime of a mutable reference
    - there is no other usable path to its referent, or to any value reachable from there
  - only references whose lifetimes may overlap with a mutable reference are:
    - those you borrow from the mutable reference itself
- variables including function arguments are dropped in reverse order
  - nested values are dropped in sourcecode order
  - fields of a struct are dropped in declaration order
  - fields of the active enum variant are dropped in declaration order
  - fields of a tuple are dropped in order
  - elements of an array or owned slice are dropped from the first element to the last
  - variables that a closure captures by move are dropped in an unspecified order
  - trait objects run the destructor of the underlying type
  - other types don't result in any further drops

``` rust
let mut x = 10;
let r1 = &x;
let r2 = &x;     // ok: multiple shared borrows permitted
x += 10;       // error: cannot assign to `x` because it is borrowed
let m = &mut x;  // error: can't borrow `x` as mutable bcoz its also borrowed as immutable
let mut y = 20;
let m1 = &mut y;
let m2 = &mut y;  // error: cannot borrow as mutable more than once
let z = y;      // error: cannot use `y` because it was mutably borrowed

// It is OK to reborrow a shared reference from a shared reference:
let mut w = (107, 109);
let r = &w;
let r0 = &r.0;    // ok: reborrowing shared as shared
let m1 = &mut r.1; // error: can't reborrow shared as mutable

// You can reborrow from a mutable reference:
let mut v = (136, 139);
let m = &mut v;
let m0 = &mut m.0; // ok: reborrowing mutable from mutable
*m0 = 137;
let r1 = &m.1;     // ok: reborrowing shared from mutable, and doesn't overlap with m0
v.1;          // error: access through other paths still forbidden

int x = 42;      // int variable, not const
const int *p = &x;  // pointer to const int
assert(*p == 42);
x++;         // change variable directly
assert(*p == 43);  // “constant” referent's value has changed


let mut x = 42;    // nonconst i32 variable
let p = &x;      // shared reference to i32
assert_eq!(*p, 42);
x += 1;       // error: cannot assign to x because it is borrowed
assert_eq!(*p, 42);  // if you take out the assignment, this is true
```

## lifetime
- name for a region of code that some reference must be valid for
- annotations:
  - some scope can be substituted for `'a` that will satisfy this signature
  - to tell how generic lifetime parameters of multiple references relate to each other
  - donot change how long any of the references live
    - rather, they describe the relationships of the lifetimes
      - of multiple references to each other without affecting the lifetimes
- every reference has lifetime
- we’re not changing the lifetimes of any values passed in or returned
  - borrow checker should reject any values that donot adhere to these constraints
- lifetimes never have any impact on machine code
  - only differing types cause Rust to compile multiple copies of a generic function
- specify lifetime parameters for functions or structs that use references
- at runtime:
  - a reference is just address
  - its lifetime is part of its type
  - lifetime has no runtime representation
- function argument and return value must have the same lifetime
- if function doesn’t return any references, then no need to write out lifetimes
  - Rust just assigns a distinct lifetime to each spot that needs one
- Rust tries to choose the smallest lifetime that works
- lifetime problem results from dangling reference or mismatch of available lifetimes
  - which cause a program to reference data other than the data it’s intended to reference
- data returned by function will live as long as data passed into function in the argument
  - first, the moves always apply to the value proper, not the heapstorage they own
  - second, rust compiler’s code generation is good at “seeing through” all these moves
    - in practice, the machine code often stores the value directly where it belongs
- functions that take references are generic, lifetimes are generic parameters
- rust ensures safety by using lifetimes instead of garbage collection, makes rust fast
-  struct lifetime annotation:
  - an instance of struct cannot outlive the reference it holds in its part field
- lifetimes on function or method parameters are called input lifetimes
- lifetimes on return values are called output lifetimes
- these rules apply to fn definitions as well as impl blocks
  - first rule, each parameter that is a reference gets its own lifetime parameter
    - function with one parameter gets one lifetime parameter:
      - `fn foo<'a>(x: &'a i32);`
    - function with two parameters gets two separate lifetime parameters:
      - `fn foo<'a, 'b>(x: &'a i32, y: &'b i32);` and so on
- second rule, if there is exactly one input lifetime parameter:
  - that lifetime is assigned to all output lifetime parameters:
    - `fn foo<'a>(x: &'a i32) -> &'a i32`
- third rule, if there are multiple input lifetime parameters:
  - but one of them is `&self` or `&mut self` because this is a method
  - the lifetime of `self` is assigned to all output lifetime parameters
  - this rule makes methods much nicer to read/write because fewer symbols are necessary
- `'static` reference can live for the entire duration of the program
- lifetime names for struct fields always declared
  - after the impl keyword and then used after the struct’s name
  - because those lifetimes are part of the struct’s type
- in method signatures inside the impl block
  - references might be tied to the lifetime of references in the struct’s fields
  - or references  might be independent
- cannot borrow mutable reference more than once at a time

## const and 'static
- const are static, immutable and inlined, simply replacing the const name with its value
  - this includes usage of constants from external crates, and non-Copy types
  - provides a convenient alternative to code duplication
  - `'static` is the only lifetime allowed for const, don't need to use it explicitly
    - valid for the entire time a program runs, within the scope they were declared
    - any references in the initializer must have 'static lifetimes
  - constants may refer to the address of other constants
    - in which case the address will have elided lifetimes where applicable
    - compiler is still at liberty to translate the constant many times
      - so the address referred to may not be stable
  - must be explicitly typed
  - use const for magic numbers and strings
  - There are no mut constants
  - const may be set only to a constant expression
    - not to the result of a value that could only be computed at runtime
  - not associated with specific memory location
  - references to same constant are not guaranteed to refer to same memory address
  - constant has no memory or other storage associated with it (it is not a place)
  - think of constant as a convenient name for a particular value
  - constant expression may only be omitted in a trait definition
  - constants can contain destructors
  - unlike associated const, free const may be unnamed by using underscore instead of name
    - as with underscore imports, macros may safely emit same unnamed const in same
      scope more than once
- const, like statics, should always be in SCREAMING_SNAKE_CASE
- `'static` variables point to a single location in memory for sharing
  - values in static variables have a fixed address in memory
  - static variable can be mutable and it is unsafe to access or modify them
  - can only store resferences with the 'static lifetime
  - unlike with constants, they can’t have destructors
  - act as a single value across the entire codebase
  - global variables are called static varibles in rust
  - `'static` reference can live for the entire duration of the program
  - all string literals have the `'static` lifetime
  - let s: &'static str = "I have a static lifetime.";
    - text of string is stored directly in the program’s binary, which is always available

## const fn
- it is a function that one is permitted to call from a const or static context
- it only restricts the types that arguments and the return type may use
- prevent various expressions from being used within it
- functions and inherent methods can be marked as const
- traits, trait impls and their methods cannot be const
- only simple by-value bindings are allowed in arguments
- by-ref bindings and destructuring can be supported
  - but they're not necessary and only complicate the implementation
- can use generic type and lifetime parameters
- complex floating point operationsa might get slightly different results
- floating point values are treated just like generic parameters
  - without trait bounds beyond `Copy`

## unsafe
- unsafe superpower includes:
  - dereference a pointer
  - call an unsafe function or method
  - access or modify a mutable static variable
  - implement an unsafe trait
  - access fields of union S
- can create raw pointers in safe code
  - but can't dereference raw pointers outside an unsafe block
- creating a pointer does not harm
  - unless we try to access value it points at might end up dealing with an invlid value
- rust borrow checker can't understand that we are borrowing different parts of the slice
  - it only know that we are borrowing from the same slice twice
- use `extern` to facilitate Foreign Function Interface (FFI):
  - way for programming language to define functions
  - enables different programming languages to call those functions
- orphan rule can be bypassed:
  - using Newtype pattern using Wrapper struct to hold instance of the type
  - if we want the Newtype to have every method the inner type has:
    - implementing `Deref` on Wrapper to return the inner type would be a solution

## types
- types are namespaces too
- rust compiler gives very few guarantees about how it lays out types
- rust provides a `repr` attribute for type definition
  - it gives in-memory representation for that type
  - helps writing rust code which interfaces with other languages
    - by using foreign-function interface
  - useful in unsafe context with raw pointers
  - useful to cast between two different types with same fields
  - eg: `repr(C)` lays out c/c++ compatible types
  - eg: `repr(transparent)`
    - used on types:
      - with single field
      - which guarantees same layout of inner and outer type
    - without which compiler cannot guarantee same layout
    - handy with **newtype** pattern for struct A and struct New(A)
      - to operate on its in-memory representation as if they were same
  - eg: `rust(Rust)` has lesser restrictions with no strict ordering of declared fields
    - it has deterministic field oredering
    - different types with same fields, types and ordering may not have same layout
  - eg: `#[repr(packed)]` for no padding in-memory representation
    - used for less memory available
    - used for low bandwidth
  - eg: `#[repr(align(n))]` to give field or type a larger alignment than required
- rust uses the u8 type for byte values
  - as reading data from file or socket yields a stream of u8 values
- **coherence**:
  - can implement trait on type only if either trait or type is local to our crate
  - called **orphan rule**, so named because the parent type is not present
- "associated methods" do not have `self` as a parameter
  - they are used for creating new instance of struct
- cannot call default implementation from overriding implementation of that same method
- char type is unicode scalar value
- rust doesn’t have reflection capabilities
  - so it can’t look up the type’s name at runtime
- if all the fields of struct are themselves `Copy`
  - make type `Copy` by placing attribute `#[derive(Copy, Clone)]` above definition
- `Copy` types are very limited in which types they can contain
- non-Copy types can use heap allocation and own other sorts of resources
- So making a type Copy represents serious commitment on the part of implementer:
  - if it’s necessary to change it to non-Copy later,
    - much of the code that uses it will probably need to be adapted
- indirection:
  - instead of storing value directly,
    - change the datastrucuture to store the value indirectly
      - by storing a pointer to the value instead

## `!` never type
- ! is empty type or never type because it has no value
- expressions that don’t finish normally are assigned the special type !
  - they’re exempt from the rules about types having to match
- expressions of type ! can be coerced into any other type
- fn exit(code: i32) -> !
    The ! means that exit() never returns. It’s a divergent function.
- `fn exit(code: i32) -> !`
  - ! means that exit() never returns
  - it’s a "divergent function"
- `continue` has a ! type
- `panic!()` has a ! type
- `loop` has a ! type since the loop never ends
- when `!` is stabilized, plan is to make `Infallible` a type alias to it

```rust
pub type Infallible = !;
```

- there is one case where `!` syntax can be used before `!` is stabilized
  - in the position of a function’s return type

```rust
trait MyTrait {}
impl MyTrait for fn() -> ! {}
```

## dynamically sized type (DST) or unsized type
- ex: str: we can't know how long the string is untill runtime
  - means can't create a variable of type str nor can take an argument of type str
- str and [T] types denote sets of values of varying sizes, they are unsized types
- we must always put values of dynamically sized types behind the pointer of some kind
- every trait is a DST we can refer to by using the name of the trait
- Rust supports polymorphism with two related features: traits and generics
- Rust can’t store unsized values in variables or pass them as arguments
  - you can only deal with them through pointers like &str or Box<Write>, which are sized
- mutable slice &mut [T] lets you read and modify elements, but can’t be shared
  - a shared slice &[T] lets share access among several readers, but not modify elements
- `Sized` trait to determine weather or not a type's size is known at compile time
  - `Sized` is automatically implemented for whose size is known at compile time
  - rust implicitly adds a bound on  sized trait to every generic function
  - All type parameters have an implicit bound of Sized
  - trait objects and slices are unsized
    - example: `dyn Iterator` or `[u8]`
  - special syntax `?Sized` can be used to remove this bound if it’s not appropriate.
  - The one exception is implicit `Self` type of a trait
  - trait doesnot have implicit `Sized` bound as this is incompatible with trait objects
  - trait need to work with all possible implementators, and thus could be any size
  - rust let you bind `Sized` to a trait
    - won't able to use it to form a trait object later
  ``` rust
     trait Foo { }
     trait Bar: Sized { }

     struct Impl;
     impl Foo for Impl { }
     impl Bar for Impl { }

     let x: &dyn Foo = &Impl; // OK
     let y: &dyn Bar = &Impl; // error: the trait `Bar` cannot be made into an object
  ```
- by default, generic function work only on types that have a known size at compile time
  - however, you can use the following special syntax to relax this restriction:
  ``` rust

     // ?Sized i.e that is may or may not be sized
     fn generic<T: ?Sized>(t: &T) {
     // the type of parameter t switched from T to &T, need some kind of pointer
     ...
     } // This syntax is only available for Sized, not any other trait
  ```
  - compiler puts the `?Sized` trait objects and slices behind fat pointers automatically
    - fat pointer is `Sized`
      - twice the size of a usize word on target platform
        - one for holding the pointer
        - one for holding extra information needed to "complete" the type
    - includes an extra word-sized field that gives additional info about pointer
      - needed by compiler to generate the code
      - for slices, the extra info is the length of the slice
      - for traits:
        1. separate implementation copy is generated for each type that uses generic code
          - extra info is that implementation address, could be multiple addresses
          - known as **static dispatch**
            - since for any copy, the address we are dispathing is known statically
          - happens at compile time
          - bcoz the cpu needs address where to jump to and continue execution
          - code transformed as non generic before optimizations
            - code for each type is optimized separately
          - this going from generic to non-generic types is **monomorphization**
          - results in increased compile time with large machine code
            - cpu's instruction cache becomes less effective
              - bcoz it needs to hold multiple copies of same instructions
                - as instructions arent shared between different instatiation
        2. **dynamic dispatch** is to call trait method on generic, unknowing actual type
          - caller tells to replace impl TraitA with &dyn TraitA
            - to make dyn TraitA `Sized`, use &dyn TraitA
            - &mut, Box and Arc can also be used for dynamic dispatch
          - caller gives address of the TraitA and of its called method
            - its a pointer to a memeory chunk called virtual method table or **vtable**
            - **vtable** holds address of all trait methods for given type
              - info about type's layout and alignment
              - fat pointer's extra word holds pointer to the vtable
          - allows to use same function body regardless of what type is called
      - use static dipatch for library and dynamic dispatch for binary
  - `Box` and `Arc` supports storing fat pointers

## trait
- rust does deref coercion when it finds types and trait implementations in three cases:
   * From &T to &U when T: Deref<Target=U>
   * From &mut T to &mut U when T: DerefMut<Target=U>
   * From &mut T to &U when T: Deref<Target=U>
- rust doesn’t try deref coercions to satisfy type variable bounds
- orphan rule: external traits cannot be implemented on external types
    * crate A defines a public trait T
    * crate B defines a public struct S
    * crate C wants to implement T for S
- you can implement:
  - one of your traits on anyone's type
  - anyone's trait on one of your types
  - but not a foreign trait on a foreign type
  - these are called the **orphan rules**
- upstream refers to something your code depends on
- downstream refers to something that depends on your code
- **blanket implementation**
  - impl<T> MyTrait for T where T: and so on
  - crate that defines a trait is allowed to write it
  - adding blanket implementation to an existing trait is considered a breaking change
- **fundamental types**
  - allow anyone to implement traits on them, even if violates orphan rule
  - bypasses orphan rule
  - are effectively erased before orphan rule is checked
  - include &, &mut and Box
  - marked with attribute `#[fundamental]`
  - adding blanket implementation over a fundamental type is considered a breaking change
  - eg: impl IntoIterator for &MyType
- **covered implementations**
  - allow implementing a foreign trait for a foreign type
  - eg: `impl From<MyType> for Vec<i32>`
    - here From and Vec are both foreign, yet there is no danger of violating coherence
    - bcoz conflicting implementation  could be added only through blanket implementation
      - in std library
      - std library cannot otherwise name MyType
  - orphan rule includes narrow exemption for this
  - `impl<P1..=Pn> ForeignTrait<T1..=Tn> for T0`
    - at least one Ti must be local
    - no T before first such Ti is one of the generic types P1..=Pn
    - generic params (Ps) are allowed in T0..Ti until covered by some intermediate type
      - T is covered if it appears  as a type pareameter to some other type like Vec<T>
      - not if it stands on its own just T or &T
    ```rust
    // valid implementations of foreign trait for foreign types
    impl<T> From<T> for MyType
    impl<T> From<T> for MyType<T>
    impl<T> From<MyType> for Vec<T>
    impl<T> ForeignTrait<MyType, T> for Vec<T>// local type is first and then T

    // invalid implementations of foreign trait for foreign types
    impl<T> ForeignTrait for T
    impl<T> From<T> for T
    impl<T> From<Vec<T>> for T
    impl<T> From<MyType<T>> for T
    impl<T> From<T> for Vec<T>
    impl<T> ForeignTrait<T, MyType> for Vec<T> // local type should come first
    ```
  - relaxation of orphan rule complicates the rule
  - adding new implementation to existing trait is non breaking only if:
    - it contains atleast one new local type
    - new local type satisfies the rules for exemption

## trait object
- an opaque value of another type that implements a set of traits
  - the set of traits is made up of:
    - an "object-safe" base trait
    - plus any number of auto trait
- it is combination of a type that implements a trait and its vtable   ?
- object-safe:
  - 2 properties for traits to satisfy in order them to be object-safe:
    1. return type isnt `Self`
    2. no generic type parameters
  - size and shape of arguments and return value should only depend on the bare trait
    - not on the instance(Self) or any type arguments (forgotten by runtime)
- its purpose is to allow abstraction across common behaviour
- we can't add data to a trait object
- generic method calls are illegal for trait object
- only one copy is generated for function that takes trait object, resulting in:
  - less code bloat at the cost of requiring slower virtual function calls
  - inhibiting any chance of inlining and related optimizations from occuring
- must be object safe because once you have used a trait object
  - rust no longer knows the concrete type that's implementing the trait
- if a trait method returns the concrete `Self` type
  - but a trait object forgets the exact type that `Self` is
    - there is no way the method can use the original concrete type
- right choice when needed collection of mixed type values, all together
- possible reason to use is to reduce the total amount of compiled code
  - may compile generic function many times, once for each type its used with
- generics have two important advantages over trait objects:
  1. speed: rust can evaluate it at compile time, so that there’s no runtime cost at all
    - rust never knows what type of value a trait object points to untill run time
      - if you pass a Sink,
        - overhead of calling virtual methods and checking for errors still applies
  2. not every trait can support trait objects, trait example: `Clone`, `Extend`
    - static methods that work only with generics
    - trait that uses `Self` type is incompatible with trait objects
- type info is lost, needed to type-check your program
- a trait object points to both
  - an instance of a type implementing our specified trait
  - **vtable**: a table used to lookup trait methods on that type at runtime
- we create a trait object by specifying some sort of pointer
  - such as & reference or Box<T> smart poiner
  - then the `dyn` keyword, and then specifying the relevent trait
- rust doesnt have inheritance but bounds
- `Trait: Sized`: only be implemented for type that already implements `Sized`
- `fn method(&self) where Self: Sized`:
  - only types that implements `Sized` can implement this method
- `dyn` is a prefix of a trait object's type
  - highlights calls to methods on the associated trait that are dynamically dispatched
- unlike generic params or `impl Trait`, compiler doesnt know concrete type being passed
  - ie: the type has been erased
- `dyn Trait` reference contains two pointers called fat pointer
  - one pointer goes to the data(eg. instance of struct)
  - another pointer points to a map of method call names to a function pointers
    - known as virtual method table or vtable
  - at runtime, when a method needs to be called on the `dyn Trait`
    - the vtable is consulted to get the function pointer
    - and then that function pointer is called
    - has additional runtime cost
    - method cannot be inlined by compiler
- `dyn Trait` produce smaller code than `impl Trait` / generic parameters
  - as the method won't be duplicated for each concrete type

## object safety
- a trait object can only be constructed out of traits that satisfy certain restrictions
  - which are collectively called 'object safety'
- object safe traits can be the base trait of a trait object
  - a trait is object safe if it has the following qualities (defined in RFC 255):
    - It must not require Self: Sized
    - All associated functions must either have a where Self: Sized bound, or
        - Not have any type parameters (although lifetime parameters are allowed), and
        - Be a method that does not use Self except in the type of the receiver.
    - It must not have any associated constants.
    - All supertraits must also be object safe.
- When there isn't `Self: Sized` bound on method,
  - type of a method receiver must be one of the types:
    - `&Self`
    - `&mut Self`
    - `Box<Self>`
    - `Rc<Self>`
    - `Arc<Self>`
    - `Pin<P>` where P is one of the types above
- when you implement a trait, either the trait or type must be new in the current crate
  - this is called the coherence rule
  - it helps rust ensure that trait implementations are unique.
  - code can’t impl Write for u8, because both are defined in the standard library
  - if allowed:
    - there could be multiple implementations of Write for u8, in different crates
    - with no reasonable way to decide which implementation to use for given method call
- can borrow a reference to a value
- references are nonowning pointers, with limited lifetimes
- like C/C++, rust puts plain string literals like "udon" in read-only memory
  - so for a clearer comparison with the C++ and Python examples
    - we call to_string here to get heap-allocated String values
- the value to the left of the dot or brackets[] is automatically dereferenced
- expressions like these three are called lvalues
  - because they can appear on the left side of an assignment:
```  rust
    game.black_pawns // struct field
    coords.1 // tuple element
    pieces[i] // array element
```
-  Each crate is a rust project

## generic trait
1. with generic type parameters
  - ex: `trait Foo<T>`
  - change must be updated to all users
  - hard to maintain
  - multiple implementations may exist
2. with associated types
  - ex: `trait Foo { type Bar; }`
  - easier to work with
  - compiler need only the type that implements the trait
  - allow users to add further associated types without affecting the users
  - will not allow multiple implementations
  - advice: use whenever you can
  - cannot implement `Deref` against multiple target type
  - cannot implement `Iterator` with multiple different `Item` types
- rule of thumb:
  1. use associated type for only one implementation of trait for given type
  2. else use generic type parameters

## std::cell::Cell
- if we can't get reference to a value then mutating it is f
- not allowed to cast a shared reference to exclusive reference
  - except `std::cell::UnsafeCell`
- Cell/RefCell allow sharing or aliasing(&T) with mutability(&mut T) in controlled manner
  - but in a single threaded way i.e. not thread safe and will give compile-time error
- dont implement `Sync` and provide interior mutability instead of inherited mutability
- RefCell enforces borrowing rules at runtime
  - the advantage is that certain memory-safe scenarios are allowed
  - whereas they are disallowed by compile-time check
- problems like 'Halting' are impossible to detect
- have single owner
- `RefCell` uses lifetime for dynamic borrowing
  - process that claim
    - temporary,
    - exclusive,
    - mutable
    - access to inner value and tracked at runtime
  - its possible to attempt to borrow a value that is already mutably borrowed
    - which results in thread pacic
- if we had not let the previous borrow of the cache fall out of scope then
  - subsequent borrow would cause a dynamic thread panic
  - this is the major hazard of using `RefCell`
- when to choose interior mutability:
  - introducing mutabilitly inside of something immutable
  - implementation details of logically-immutable methods
  - mutating implementations of `Clone`

## std::boxed::Box<T>
- don't have performance overhead other than storing data on heap instead of stack
- situations to use Box
  - when size not known at compile time
    - want to use that value of that type in a context that requires exact size
  - when you have a large amount of data
    - want to transfer ownership but ensure the data won't be copied when you do so
  - when you want to own a value
    - you care only that it's a type that implemants a particular trait
      - rather than being a specific type
- the reason the deref method returns a reference to a value and then the plain
  dereference outside the patentheses in `*(y.deref())` is still necessary, is the
  ownership system
  - if the deref method returned the value directly instead of reference to the value,
    - the value would be moved out of the self
  - so we do not want to take the ownership of the inner value
- deref coercion works only on types that implement the `Deref` trait
  - deref coercion converts such a type into a reference to anther type
  - For ex. deref coercion converts &String to &str
    - because String implements the Deref trait such that it returns str
  - deref coercion happens automatically:
    - when we pass a reference to a particular type's value as an argument to a function
    - or method that doesn't match parameter type in the function or method definition
  - sequence of calls to deref method converts type we provided into type parameter needs
- the Deref trait is defined for the types involved
  - rust will analyze the types and use Deref::deref as many times as necessary
    - to get a reference to match the paramerter's type
  - number of times Deref::deref needs to be inserted is resolved at the compile time,
    - so there is no runtime penalty for taking advantage of deref coercion
- rust does deref coercion when it finds types and trait implementations in three cases:
  - From &T to &U when T: Deref<Target=U>
  - From &mut T to &mut U when T: DerefMut<Target=U>
  - From &mut T to &U when T: Deref<Target=U>
- third case is trickier:
  - Rust coerce mutable reference to immutable one but reverse is not possible
  - because of the borrowing rules,
    - if you have a mutable reference,
      - that mutable reference must be the only reference to that data
        - otherwise the program wouldn't compile
  - converting mutable to immutable reference never breaks borrowing rule
  - converting immutable to mutable reference would require that
    - the initial immutable referenc is the only immutable reference to that data
      - but the borrowing rules don't guarantee that
  - therefore, rust can't make the assumption that
    - converting an immutable reference to a mutable reference is possible
- variables are dropped in the reverse oreder of their creation
- rust doesn't let you call the Drop trait's drop method manually
  - instead call std::mem::drop function for forced drop before the end of its scope

## std::rc::Rc<T>
- works in single threaded scenario

## trait std::iter::Iterator
- iterator: way of processing a series of elements
  - produce a series of values
  - lazy and do nothing unless consumed
- more calls than its size will always return `None`
- `collect()` on an iterator turns it into a collection
- has only one `Item` type therefore associtative types are used
  - Type checking becomes less complicated
- Range<type> is an iterator
- `size_hint()` returns a tuple
  - the first element is the lower bound
  - the second element is the upper bound
  - the second half of the tuple that is returned is an `Option<usize>`
  - `None` here means either
    - no known upper bound
    - or the upper bound is larger than `usize`
- a value of type `Self` cannot be built from an iterator
  - over elements of type `A`: `std::iter::Iterator<Item={A}>`
- by implementing `IntoIterator` your type will work with for loop syntax
- std collections are all iterable, as are arrays and slices

``` rust
trait Iterator{
    type Item; // type of value the iterator produces

    // returns iterator's next value Some(value)
    // or None to indicate end of sequence
    fn next(&mut self) -> Option<Self::Item>;

    ..// many default methods
}
```

- There are three common methods which can create iterators from a collection:
  - `iter()` iterates over &T
  - `iter_mut()` iterates over &mut T
  - `into_iter()` iterates over T
- **iterator adapter**: functions which take an `Iterator` and return another `Iterator`
  - they are form of **adapter pattern**

## closure
- closures: anonymous function-like construct you can store in a variable
- it borrows:
  - when rust creates a closure, it can automatically borrow a ref to a captured variable
  - closure refers to a captured variable, so it must have a ref to it
  - cannot outlive captured reference's lifetime, therefore no gc required
- types are locked into the closure in first use
  - we get a type error if we try to use a different type with the same closure
- rust offers two ways for closure to get data from enclosing scopes:
  - borrowing
  - moves
- moving a copyable type like i32 will be copied instead
- moving contributes to thread safety as there is no sharing
- function has types eg: type fn(&City) -> i64
  - `fn(&City) -> bool` // fn type (implemented by functions only)
  - `Fn(&City) -> bool` // Fn trait (implemented by both functions and closures)
    - this trait is automatically implemented by all the functions and closures
  - closure is callable but its not a fn
- function value is memory address of its machine code, just like function pointer in c++
- faster and safer than function pointers
- fast enough that can be used in performace sensitive code
- aren't allocated on heap unless you put them in Box, Vec, or other container
- whenever compiler knows the type of closure you are calling
  - it can inline the code for that closure
    - this makes it ok to use closures in tight loops
    - often the compiler can inline all calls to a closure
    - doesn’t contain a pointer to its code! that’s not necessary
    - knows which code to run when called
``` rust
// Pseudocode for `Fn`, `FnMut` and `FnOnce` traits with no arguments.

trait Fn() -> R {
    fn call(&self) -> R;
}

trait FnMut() -> R {
    fn call_mut(&mut self) -> R;
}

trait FnOnce() -> R {
    fn call_once(self) -> R;
}

```
( Fn ) FnMut ) FnOnce )
- Fn is subtrait of FnMut, which is a subtrait of FnOnce
- every Fn meets the requirements for FnMut
- every FnMut meets the requirements for FnOnce
- Fn is the family of closures
  - functions that you can call multiple times without restriction
  - this highest category also includes all fn functions
  - closure that drop values, are not Fn, only FnOnce
- FnMut: trait of closures that requires mut access to a value
  - but doesn’t drop any values
  - can be called multiple times if the closure itself is declared mut
- FnOnce: trait of closures that can be called once
- every closure has its own type
  - so every closure has an adhoc type created by the compiler
    - large enough to hold that data
  - no two closures, even if identical, have the same type
  - need to use boxes and trait objects for more than one type of closure as parameter
    - not safe to store closure if
      - it contains borrowed references to variables that are about to go out of scope
        - therefore 'static lifetime is added
  - code that works  with closures usually needs to be generic
- Clone and Copy closure:
  - non move
  - doesnt mutate variables
  - holds only shared references
- if everything a move closure captures is Copy, its Copy; if its Clone, its Clone

## variance
- fn flips the variance of args
- mutable refs are invariant in their arg type

## refutability
``` rust
// irrefultable pattern
let some_option_value: Option<i32> = None;
let Some(x) = some_option_value;

// refutable pattern
let x = 4;
```
- if let and while let are both refutable and irrefutable
- gives warning if it doesn’t make sense to use if let with an irrefutable pattern
  - ex: if let x = 5 { ... };
## Marker traits: `std::marker::`
- indicate property of implementing type
- no methods or no associated types, just empty traits
- serve just to tell that particular type can or cannot be used in certain way
- eg: `Send`, `Sync`, `Copy`, `Sized` `Unpin` in std::marker module
- except `Copy`, all markers are auto trait
- auto trait are automatically implemented by compiler
  - unless the type contains something that doesnot implement marker trait
- allow to write bounds that capture semantic requirements not directly expressed in code

## marker types
- are unit types
- hold no data and have no methods
- useful for marking a type as being in particular state
- to make impossible for a user to misuse an API

## std::marker::PhantomData<T> and Zero Sized Type
- where T: ?Sized
  - `PhantomData`: zero-sized type used to mark things that “act like” they own a T
- tells compiler that:
  - your type acts as though it stores a value of type T, even though it doesnt
- if your struct does not own the data of type T:
  - it is better to use a reference type
  - like `PhantomData<&'a T>` (ideally)
  - or `PhantomData<*const T>` (if no lifetime applies)
  - so as not to indicate ownership
- phantom type parameter: is simply a type parameter which is never used

## std::marker::Send
- ownership of values of the type implementing `Send` can be moved between threads
- almost every rust type is `Send`
  - except `Rc<T>`, `Mutex<T>`, `*mut u8, GenericError`
  - `Rc` is neither `Send` nor `Sync`
    - use mutability in way that isnt thread-safe
- types composed entirely of types that are `Send` are also `Send`

## std::marker::Sync
- types that implement `Sync` are safe to pass by non mutable reference to another thread
  - they can be shared across threads
  - safe here means free form data races and undefined behaviour
- it is safe for type implementing `Sync` to be referenced from multiple threads
- any type T is `Sync` if &T is `Send`
  - means the reference can be sent safely to another thread
- types composed entirely of types that are `Sync` are also `Sync`
- `RefCell<T>`, the family of related `Cell<T>` types are not `Sync`
- `std::sync::mpsc::Receiver` is `Send` but not `Sync`
  - it guarantees that receiving end of mpsc channel is used by only one thread at a time

## std::ptr
- a null pointer is never valid, not even for accesses of size zero
- for a pointer to be valid, it is necessary, but not always sufficient
  - the pointer be dereferenceable
  - memory range of given size starting at pointer must be within bounds of single
    allocated object
  - every (stack-allocated) variable is considered a separate allocated object.
- even for operations of size zero, the pointer must not be pointing to deallocated memory
  - deallocation makes pointers invalid even for zero-sized operations
  - casting any non-zero integer literal to a pointer is valid for zero-sized accesses
    - even if some memory happens to exist at that address and gets deallocated
    - this corresponds to writing your own allocator
      - allocating zero-sized objects is not very hard
      - `NonNull::dangling`: canonical way to obtain a pointer valid for zero-sized access
- All accesses performed by functions in this module are non-atomic
  - it is undefined behavior to perform two concurrent accesses unless both are read only
  - this explicitly includes read_volatile and write_volatile
  - volatile accesses cannot be used for inter-thread synchronization
- result of casting a reference to a pointer is valid for:
  - as long as the underlying object is live
  - and no reference (just raw pointers) is used to access the same memory

## raw unsafe pointers or primitive type pointers
- `*const T` is immutable
- `*mut T` is mutable
```rust
    // can create raw pointers in safe code
    let mut num = 5;
    let r1 = &num as *const i32;
    let r2 = &mut num as *mut i32;

    let address = 0x012345usize;
    let r = address as *const i32; // raw pointer to an arbitary location
```
- cannot dereference raw pointers outside an unsafe block
- allowed to ignore the borrowing rules
  - by immutable and mutable pointers or multiple mutable pointers to the same location
- are not guaranteed to point to valid memory
- can be null
- donot implement any automatic cleanup

## Cargo.toml
- two build profiles:
  - [profile.dev]
  - [profile.release]

## char
- 32 bit holding Unicode point from 0 to 0xd7ff or 0xe000 to 0x10ffff
- implements `Copy`, `Clone`, `PartialEq`,`Eq`, `PartialOrd`, hashing, formatting etc
- all ASCII methods are available on `u8` byte type
```rust
assert!(32u8.is_ascii_whitespace());
assert!(b'9'.is_ascii_digit());
```

## ASCII, Latin-1 and Unicode
- 0 to 0x7f Unicode and ASCII match(also UTF-8)
- 0 to 0xff Unicode and ISO/IEC 8859-1(8bit super set of ASCII)
  - Unicode calls this range of code points the Latin-1 code block
  - Unicode is superset of Latin-1
  - for use with western european languages
- range of bytes holding ASCII text is valid UTF-8
  - reverse is also true if UTF-8 includes only characters form ASCII
  - not true for Latin-1

## alloc::string::String
- represents text using UTF-8
  - UTF-8 encodes a character as a sequence of one to four bytes
- implemented as wrapper around `Vec<u8>` ensures wellformed UTF-8
- shares Vec performance characteristics
- Use the owned String for building and mutating strings
- For converting to strings use the `format!` macro
- for converting from strings use the `FromStr` trait
- dereferences to `&str`
- method defined on `str` is directly available on `String`

## str
- a UTF-8 string slice
- a primitive type
- typically accessed as immutable references: &str
- `slice.chars()` produce Iterator over its characters
- if a type implements `Display`, std lib automatically implements `std::str::ToString`

## std::sync::{Mutex, RwLock, CondVar, atomic}
- `Mutex`: solely about locking data in multi-threaded environment
  - provides exclusive (mut) access to its locked data
    - even though the data has shared access
    - dynamically enforces exclusive access
      - something thats usually done statically at compile time
    - even `std::cell:RefCell` does the same without supporting multiple threads
    - both are flavous of interior mutability
  - `datarace`: a bug known for reading and writing the same memory concurrently
  - `poisoned mutex`:
    - if a thread panics while holding a mutex
      - rust marks the mutex as poisoned
    - any attempt to lock the subsequent poisoned mutex will get an error result
  - can have only one reader or one writer or none
- `RwLock` have either one writer or many readers
- in `CondVar` *wait()* blocks untill some other thread calls *notify_all()*
- `atomic` types are for lock free concurrent programming
  - AtomicIsize, AtomicUsize, AtomicI8, AtomicI16, AtomicI32, AtomicI64, AtomicU8
    - shared integer types corresponding to single-threaded isize, usize, i8, i16 etc
  - AtomicBool is a shared bool value.
  - AtomicPtr<T> is a shared value of the unsafe pointer type  \*mut T
  - their constructors are all const fn for creating static variables as well
- RwLock, Mutex and Atomics are forms of interior mutability
  - so their method takes self by shared reference
  - this makes them useful as simple global variables

## std::sync::{Rc, Arc}
- `Rc` and `Arc` types are very similar
  - only difference is: Arc is safe to share between threads directly
    - keep the containing variable alive, even if the parent thread bails out early
    - `clone()` only increases the reference count of `Arc` not the containing variable
  - plain Rc uses faster non- thread-safe code to update its reference count

## async
- `Futures` are inert and make progress only when polled
  - dropping a future stops it from making further progress
- `Future` runs on executor
- async is zero-cost in rust, which means only pay for what you use
- can use async without heap allocations and dynamic dispatch
  - which is great for performance
  - this also lets you use async in constrained environments, such as embedded systems
- no built-in runtime is provided by rust at global level
  - instead, runtimes are provided by community maintained crates locally
- both single and multithreaded runtimes are available
- runtime uses small amount of expensive threads to handle large amount of cheap tasks
- results in larger binary blobs due to the state machines generated from async functions
  - since each executable bundles an async runtime
- traits doesnt have asynchronous methods
- only free functions and functions inherent to a specific type can be asynchronous
- `async-std::task::block_on()`:
  - synchronous fn that produces final value of asynchronous fn
  - its an adapter from asynchronous to synchronous world
  - it blocks entire thread until the value is ready
  - use `await()` instead
- spawn requires the future to be `Send` implemented
- spawn is used for thread-pool
- spawn_local to run on current thread even without Send
- for long running computation use:
  - async_std::task::yield_now() or async_std::task::spawn_blocking()
- new failure modes:
  1. call a blocking fn
  2. implement Future trait incorrectly
  - such errors silently pass compiler and unit test
- with `wake()`, the executor knows exactly which futures are ready to be polled
### async/.await
- `await` keyword suspends execution until the result of a Future is ready
- `async` keyword transform block of code into a state machine that implements `Future`
- async/.await yield control of the current thread rather than blocking
  - allowing other code to make progress while waiting on an operation to complete
- async fn and async block both returns a value that implements `Future`
- async fn which take references or other non-'static arguments return a Future
  - it is bounded by the lifetime of the arguments
``` rust
async fn foo(x: &u8) -> u8 { *x } // This function:

// Is equivalent to this function:
fn foo_expanded<'a>(x: &'a u8) -> impl Future<Output = u8> + 'a {
    async move { *x }
}
```
- `Future` returned from an `async fn` must be `.await`ed
  - while its non-'static arguments are still valid
    - storing `Future` or sending it over to another task or thread, this may be an issue
  - workaround for turning `async fn` with references-as-arguments into a 'static future:
    - to bundle the arguments with the call to the `async fn` inside an async block
    - by moving the argument into async block,
      - this extend its lifetime to match that of the `Future` returned from call to good
``` rust
fn bad() -> impl Future<Output = u8> {
    let x = 5;
    borrow_x(&x) // ERROR: `x` does not live long enough
}

fn good() -> impl Future<Output = u8> {
    async {
        let x = 5;
        borrow_x(&x).await
    }
}
```
- `async` block creates an anonymous type that implements `Future`
- `async move` block will take ownership of the variables it references
  - allowing it to outlive the current scope
  - but giving up the ability to share those variables with other code
- variables used in async bodies must be able to travel between threads
  - unlike Rc, &RefCell or any other types that don't implement the Send trait
    - references to types that don't implement the Sync trait
    - can use these types as long as they aren't in scope during a call to .await
- hold a traditional non-futures-aware lock across an .await
  - as it can cause the threadpool to lock up or deadlock
  - use the Mutex in futures::lock rather than the one from std::sync
### move
- `move` carries with it the semantics of ownership transfer from one variable to another
  - which is the key difference between a Copy and a move
- all values in rust are trivially moveable
  - means address of a value is not necessarily stable in between borrows
  - rust can move values without notifying them that they have moved
    - by assignment or `mem::replace`
- compiler is allowed to move a value to a new address
  - without running any code to notify that value that its address has changed
  - although compiler will not insert memory moves where no semantic move has occurred
    - there are many places where a value may be moved
    - example, when doing assignment or passing a value into a function
    - Box<T> and &mut T also allow moving the underlying value they point at
    - can move out of a Box<T>, or use mem::replace to move a T out of a &mut T
    - so putting value behind a pointer may not ensure fixed address at a memory location
    - to get fixed address at a memory location for a value, pinning is necessary
### std::pin::Pin
- types that pin data to a location in memory
  - from the time it is pinned until its drop is called
  - so that fully safe code cannot move
- used for address-sensitive states like:
  - self-referential types and intrusive data structures
  - example for self-referential types
    - the state machines generated by the compiler to implement Future for async fns
- `Pin<Ptr>` can wrap any pointer type
  - forming a "promise" that the pointee will not be moved or otherwise invalidated
    - in fully safe code only
  - thing wrapped by Pin is not the value which we want to pin itself,
    - but rather a pointer to that value
    - Pin<Ptr> does not pin the Ptr; instead, it pins the pointer’s pointee value
- cannot mutably dereference a `Pin<Ptr>` unless the pointee is `Unpin` or we use unsafe
- pinning is a specific contract between the unsafe parts of a library API and its users
- to poll futures, they must be pinned using a special type called `Pin<T>`
- pin works in tandem with `Unpin` marker
- pin guarantees that an object implementing `!Unpin` will not ever be move
  - vice-versa for `Unpin` even if pinned
- pinning an object to the stack will always be unsafe if our type implements `!Unpin`
  - for pinning to stack, use `pin_utils` crate to avoid writing unsafe code
- `std::marker::PhantomPinned` a marker type for which doesnot implement `Unpin`
  - if a type contains a PhantomPinned, it will not implement Unpin by default
  - it removes auto-implemented `Unpin` bound to mark this type as address-sensitive state
- in unsafe code, the user is responsible to not to move its value
- pinned data must be dropped before it is invalidated
  - to overwrite a pinned value, its `drop` must be called beforehand
    - to prevent memeory leaks
    - as a matter of soundness

## unsafe
- unsafe superpowers are that are not checked by compiler for memeory safety:
  1. dereference a raw pointer
  2. call an unsafe function or method
  3. access or modify a mutable static variable
  4. implement an unsafe trait
  5. access fields of a union
- keep unsafe block small
- enclose unsafe code within a safe abstraction and provide a safe api

## macro
- generic syntax extension form
- macros are expanded into abstract syntax trees
  - rather than string preprocessing
  - so you don't get unexpected precedence bugs
- instead of generating a function call, macros are expanded into source code
- general mechanism macros are built on is "syntax extensions"
- macros that are visible in one module are automatically visible in its child modules
  - use `#[macro_use]` to export macros form a module "upward" to its parent module
    - eg: `#[macro_use] mod some_macro;`
  - `#[macro_export]` makes macro automatically `pub`
    - can be referred to by path,like other items
- first stage of compilation for a Rust program is tokenization
  - `self` is both an identifier and keyword
  - rustc_lexer emits single character symbols as tokens
  - rustc_parse emits multi character symbols as tokens
- second is parsing: stream of tokens is turned into Abstract syntax Tree(AST)
- 4 syntax extension form:
```rust
1.  # [ $arg ]; e.g. #[derive(Clone)], #[no_mangle], …
2.  # ! [ $arg ]; e.g. #![allow(dead_code)], #![crate_name="blang"], …
3.  $name ! $arg; e.g. println!("Hi!"), concat!("a", "b"), …
4.  $name ! $arg0 $arg1; e.g. macro_rules! dummy { () => {}; }
```
  - 1 n 2 are attributes which annotates items, expressions and statements
    - classified into 3:
      - **built-in attributes**: implemented by compiler
      - **proc-macro attributes**: implemented by procedural macros
      - **dervie attributes**: implemented by procedural macros
  - 3 are function like macros
      - use with `macro-rules!`, `macro` and procedural macros
      - a generic syntax extension form
      - the arg is single non leaf token tree
  - 4 is variation not available to macros, used with `macro-rules!`
- syntax extensions are parsed as part of AST and

| can appear in:             | not in        |
|----------------------------|---------------|
| patterns                   | identifiers   |
| statements                 | match arms    |
| expressions                | struct fields |
| items(includes impl items) |               |
| Types                      |               |

- expansion of all syntax extensions:
  - after construction of the AST
  - but before compiler begins constructing semantic understanding of program
  - expansion involves:
    - traversing the AST
    - locating syntax extension invocations
    - finally replacing them with their expansion
    - syntax extension recursion limit defaults to 128
    - limit can be raised by `#![recursion_limit="…"]` attribute crate-wide
- **Hygiene**: ability for a macro to work in its own syntax context:
  - not affecting nor affected by the surroundings
  - syntax extension should be invocable without interfering with its surroundig context
  - affects identifiers and paths emitted by syntax extensions
  - identifier created by syntax extension should not be accessed by environment
  - identifire used in syntax extension should not reference outside of it
- `create` and `use` refer to the position the identifier is in ie:
``` rust
// introduce something new under the name therefore hygiene
  - Foo in struct Foo {} or the foo in let foo = …;

// referring to something existing therefore not hyginen
  - but Foo in fn foo(_: Foo) {} or foo in foo + 3
```
- **Debugging**:
  - expand code via the unstable `rustc +nightly -Zunpretty=expanded hello.rs`
  - expand code via `cargo expand` by installing **cargo-expand** crate
- `macro_rules!`:
  - technically not part of rust syntax
  - `($matcher) => {$expansion};` for each rule
  - any type of paranthesis can be used
  - expansion part of rule is called its _transcriber_

## & vs ref
* `&` denotes that your pattern expects a reference to an object. Hence `&`
   is a part of said pattern: `&Foo` matches different objects than `Foo` does.
* `ref` indicates that you want a reference to an unpacked value. It is not
   matched against: `Foo(ref foo)` matches the same objects as `Foo(foo)`.
   - `ref` annotates pattern bindings to make them borrow rather than move
     - it is **not** a part of the pattern as far as matching is concerned
     - it does not affect *whether* a value is matched, only *how* it is matched

## cargo
- when compiling libraries, cargo uses `--crate-type lib` option
  - which tells rustc not to look for main() but instead produce an .rlib file
- `cargo test -- --nocapture` to enable print statement
- `cargo build --release` produces optimized build
- release builds run faster, but takes longer to compile
  - they don't check for integer overflow
  - skip `debug_assert!()`
  - stack traces they generate are less reliable

| command line          | cargo.toml section |
| ----                  | ----               |
| cargo build           | [profile.debug]    |
| cargo build --release | [profile.release]  |
| cargo test            | [profile.test]     |
|                       |                    |

``` toml
[profile.release]
debug = true  #enable debug symbols in release builds
```
- the debug setting controls -g option to rustc
- `cargo test math` runs all tests that contain math somewhere in their name
- `cargo test` - to run all test
- `cargo test --doc` to only run documentation test
- add a `rust-toolchain.toml` file in that project with:

``` toml
[toolchain]
channel = "nightly"
```
- or `rustup override set nightly` command for particular project dir

``` Cargo.toml
[package]
name = "fun_game" # for crate
version = "0.1.0"
edition = "2022"
description = "A fun game where you guess what number the computer has chosen."
license = "MIT OR Apache-2.0"

[dependencies]
add_one = { path = "../crate1" }
```

- at workspace level
  - has only one `Cargo.lock` file for whole project rather than having in each crate dir
  - donot have a `[package]` section in Cargo.toml
``` Cargo.toml
[workspace]
members = [
    "crate1",
    "crate2",
    "fun_game",
]
```
- workspace structure
```
├── Cargo.lock
├── Cargo.toml
├── crate1
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
├── crate2
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```

## doc
- `cargo doc` to build documentation in target/doc
- `cargo doc --open` creates and open doc
- `cargo test --doc` to only run documentation test
- `///` generate library doc for following item
- `//!` generate library doc for enclosing item
- `src/lib.rs` file is crate root

## miscellaneous
- reflexive, means `Into<T> for T` is implemented
- never implement `PartialEq` for a foreign type
  - do impl PartialEq<ForeignType> for LocalType
  - but it should not do impl PartialEq<LocalType> for ForeignType.
  - this avoids the problem of transitive chains that criss-cross crate boundaries
- NaN is unequal to every other value
- instead of const fn `lazy_static!` is used to initialize static variables
  - incurs tiny performance cost for each access to static data
- `'static` is default for `-> impl` return type, so omitting it would have no effect

## commands
- `rustc --print sysroot`
- `rustup override set nightly` for particular project only
- `rustup toolchain list`
- `export RUSTFLAGS="-Z macro-backtrace"` for more info while debugging in nightly
- `cargo build --verbose` to see how cargo is invoking rustc
- copy rustc commandline and add `-Z unstable-options -- pretty expanded` as options
  - fully expanded code will be dumped to terminal
  - works only if code if free of syntax errors
- `#![feature(log_syntax)]` flag with `log_syntax!()` prints args for macro debugging
- `trace_macro!(true)` (maybe nightly) tells compiler to log all macro calls

## unreachable!(), unimplemented!(), todo!()
