# Basic Elements

The elements below, although simple, are an important starting point to code. Most of these concepts are present in other languages.

## Primitives Types

This section is non-exhaustive by a matter of simplicity. There are other primitive types beyond these and you may find information about them on the [primitive types section in the standard library's documentation](https://doc.rust-lang.org/std/index.html#primitives).

### **Integers**

- Signed: `i8`, `i16`, `i32`, `i64`, `i128` e `isize`. (`i` from _integer_).
  - e.g.: `0`, `-5`, `1_000_000i32`
- Unsigned: `u8`, `u16`, `u32`, `u64`, `u128` e `usize`.
  - It needs to be non-negative. e.g.: `0`, `10`, `42`, `1_000_000usize`

The number in the type indicates how many bits are used by the integer. This has direct relation with the range of values which are possible to represent on that type. For example, `u8` can only represent a range of 2<sup>8</sup> values.

If the type of the integer literal is not explicitly annotated or cannot be inferred, it will be `i32` by default.

### **Floats**

`f32` e `f64`. Examples: `1.0`, `-15.50f32`

Floating point numbers can represent decimal numbers (with an impression inherent of the floating point representation in the _hardware_). _Floats_, as defined by IEEE-754, can be more a bit more painful to work with than what you're used to in other languages. Rust makes explicit some footguns that could pass unnoticed.

### **bool**

Can be `true` or `false`.

### Unit

This is the type of the empty tuple `()`, which is the only value of this type. It is used in places where there's no meaningful value defined. For example: functions which "return nothing" actually return `()`.

## Variables

A variable stores a value and can be manipulated through its identifier. In Rust, we use `let` to define variables. `let` works with pattern matching, but for now we're not going to take advantage of it to keep things simple.

Below there's the skeleton of declaration and binding of variables.

```rust,ignore
// Declaration
let <identifier>: <type>;

// Declaration with value binding
let <identifier>[: <type>] = <expression>;
```

Rust variables are immutable by default. If you try to change an immutable variable's content, the compiler will complain:

```rust,compile_fail
// If you try to run it, You'll see that this code doesn't compile.
let x = 5;
x = 10;
```

As our dear compiler suggests, just add `mut` before the identifier to make the variable mutable.

```rust
// It'll work this time!
let mut x = 5;
x = 10;
assert_eq!(x, 10);
```

It's important to note that Rust has type inference. This means that it tries to identify undeclared types by using the types of the expressions present in the code. For example:

```rust,ignore
let is_raining = true;
```

It's not necessary to annotate the type. As `true` is of type `bool`, `is_raining` will also be.

## Operators

A complete list can be found [on this Rust Book appendix](https://doc.rust-lang.org/book/appendix-02-operators.html).

### Arithmetic

`+`, `-`, `/`, `*`, `%`

In case you don't know, `%` is the division remainder of the first number by the second one.

These operators can be used together with assignment. For example:

```rust
let mut x = 2 + 2;
x += 1;
assert_eq!(x, 5);
```

### Logic

- Negation: `!`
- logic AND: `&&`
- logic OR: `||`

```rust
let x = true && !false;
assert_eq!(x, true);
```

### Relational

`<`, `<=`, `==`, `!=`, `>=`, `>`

```rust
let x = 5 <= 10;
assert_eq!(x, true);

let y = 3 < 1;
assert_eq!(y, false);
```
