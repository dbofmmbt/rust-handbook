# Ownership and Borrowing

If you ask me: "what is Rust's most innovative feature?", my answer would be _ownership_. This concept allows the language to be memory-safe without a garbage collector. In practice, you have the performance of a systems language without giving up reliability.

If you came from languages that are not C or C++, it means that now you have a systems language that feels like programming in the high-level. Anyone can write efficient code without the fear of accidentally corrupt the memory.

If by chance you now C or C++, you probably needed to debug a series of errors from manual memory management. You'll be happy to know that `rustc` simply rejects programs which can cause these errors with helpful, instructive messages, explaining you what could go wrong.

## The Concept

The ownership rules are pretty simple:

- Every value has a variable which is its owner.
- the value's ownership can be moved.
- When the owner goes out of the scope, the value is released.

```rust,editable
fn main() {
    let x = String::from("hello"); // x is the owner of the string
    // ...
    println!("Initially, x is owner of: {x}");
    // ...
    let y = x; // The string was moved to `y`. `x` stopped being the owner.
    // From this point, trying to access the string through `x` is an invalid operation, since the string was moved.
    // ...
    // println!("x is owner of: {x}"); // invalid!
    // ...
    println!("Now, y is owner of: {y}");
    // ...
    // At the end of this function, `y` goes out of scope and the string will be released.
}
```

Besides ownership, we have borrowing:

- The value's owner can borrow it through shared references (immutable) or unique references (mutable).
- At any moment, there can only be one unique reference or any number or any number of shared references.
- References are always valid. They can't be used after the value they reference is moved or released.

That's it. By following these rules, we are free from any trouble involving allocation and release of resources. **And the best part**: the compiler check if we're following them automatically.

Illustrating with an example:

```rust,editable

struct Book {
    title: String,
}

fn shared(book: &Book) {
    println!("[shared] The book's title is: {}\n", book.title);
}

fn unique(book: &mut Book) {
    println!("[unique] The book's title was: {}", book.title);
    book.title.push_str(" part 2");
    println!("[unique] Now the title is: {}\n", book.title);
}

fn be_owner(book: Book) {
    println!("[be_owner] I'm the owner of the book: {}\n", book.title);
}

fn main() {
    let mut first_owner = Book {
        title: String::from("How to make friends and influence people") // recommended
    };

    // Here, we are lending a shared borrow of the book to the variable `first_borrow`
    let first_borrow = &first_owner;

    // We create a second shared reference to pass to the function `shared`
    shared(&first_owner);

    // That's okay if you lend a mutable borrow....
    unique(&mut first_owner);

    // But you cannot access `first_borrow` anymore!
    // println!("title: {}", first_borrow.title);

    // But there's no problem to create a new shared reference.
    let second_borrow = &first_owner;

    // The idea is that if you have a shared reference, you cannot access it after it could've been changed (e.g. by a mutable reference or when the value is moved to other owner)

    // Everything okay!
    shared(second_borrow);

    // Ownership moved to other owner.
    let second_owner = first_owner;

    // All the preceding references stopped being valid because of the move. The old owner stopping being accessible too!
    // shared(second_borrow);
    // unique(&mut first_owner);

    println!("[main] second owner: {}\n", second_owner.title);

    be_owner(second_owner);

    // The book is not accessible here, since its ownership was passed to the `be_owner` function. At this point, the value has been released. `second_owner` is not accessible too.
    // let invalid_borrow = &second_owner;
}
```

Feel free to play with this example. Notice the error messages. They are very descriptive and were made to help you having a better understanding of why a given piece of code is invalid.

All you have seen here affects the whole language. With time, these concepts become natural and you'll start to agree with the borrow checker (part of `rustc` which checks if you're following the rules) instead of fighting it. I even miss these concepts when I go back to other languages, because it's usually easier to understand what's happening in the source code.

Maybe some people will notice that certain things which cause errors in the `Book` example are possible with integers and floats. The reason is that these types are cheap to copy. So, instead of moving them, the value is copied. It happens because they implement the trait `Copy`. We'll talk about traits ahead, but they're similar to interfaces in other languages.

Besides that, I omitted the lifetimes concept in purpose. They are necessary for the compiler to guarantee that the references are always valid. However, it seemed more appropriate to get into this concept after explaining generic code, as a lifetime is a sort of generalization over how long a reference is valid.

## What `rustc` protects you from

The following examples are written in C, because it allows to illustrate well how you could cause undefined behavior (UB) in your program, which is basically an elegant way of telling that it can, under any execution, make your application:

- Abort execution
- Run normally
- Run, but process invalid values

When you got UB, the best to happen is the execution to be aborted as soon as the corruption occurs. This way, you can try to identify where the improper memory use occurred. Nonetheless, it can work on your machine, but not in production. Yeah, very fun.

Personally, I just understood all the memory problems below after learning Rust. Before that, I just stumbled on them without fully understanding the kind of error that was happening on my program. In my C learning, no reference I used taught me about those classes of errors in a clear way.

### use after free

It consists in using a memory which you already released to the system. When a memory region is no longer yours, there's no guarantee that you're accessing valid information.

```c
void main() {
    int *p = malloc(sizeof(int));
    // ...
    free(p);
    *p = 123; // Writing on a released memory region! UB!
}
```

In the best case, you'll get a segmentation fault. In the worst, `*p`'s content will be overwritten and you'll process garbage, making the execution fails in other part of the code, or even worse: your program may generate and invalid output. I lost two nights of sleep on this kind of error once.

In Rust, it doesn't even compile:

```rust,compile_fail
fn main() {
    let x = String::from("ferris!");
    drop(x); // The string's ownership has been moved to `drop`
    // ...
    println!("{x}");
}
```

### double free

The same memory address is released twice.

```c
void main() {
    void *p = malloc(500);
    // ...
    free(p);
    // ...
    free(p); // Same address released more than once! UB!
}
```

It looks improbable to occur, but it can be easy to introduce on a refactor.

In Rust, it doesn't even compile:

```rust,compile_fail
fn main() {
    let x = String::from("ferris!");
    drop(x); // The string's ownership has been moved to `drop`
    // ...
    drop(x); // It isn't possible to pass to `drop` (or to any other fn) a value which has been moved.
}
```

### Dereferencing a null pointer

[The 1 billion dollars mistake](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions). Even high-level languages, with garbage collection, allow this memory error.

```c
void main() {
    void *p = malloc(500); // this function may return NULL in case memory hasn't been allocated successfully
    // ...
    *p = 123; // Following a NULL pointer! UB!
}
```

The good side of this error is that it normally aborts the execution instead of processing invalid data. The bad side... This is an error class which is totally avoidable, even without ownership. The sooner errors are fixed, lower are the costs. If your tools stop the introduction of certain kinds of defects, that's even better.

In Rust... There's no null reference. We use `Option<T>` to represent that a value of type `T` can be present (`Some(value)`) or not (`None`).

```rust,editable
fn main() {
    let x: Option<String> = Some(String::from("ferris!"));
    // ...
    // I can't access string directly without first checking if `x`, which is a `Option<String>`, is `Some(...)` or `None`.
    match x {
        Some(value) => println!("I found this: {value}"),
        None => println!("Nothing here!"),
    }
}
```

### Memory leak

Technically, leaking memory isn't considered UB. However, it consumes system resources unnecessarily.

```c
void main() {
    void *p = malloc(500000000);
    // Using p...
    // ...
    // I don't need `p` anymore, but I forgot to `free` it...
    // ...
}
```

In Rust, memory will be released automatically once the owner goes out of scope. Even though leaking memory is something safe to do in Rust (i.e. someone could leak memory explicitly if they want/need without using `unsafe`), most of the time it'll be released automatically due to the ownership concept.

### Accessing an invalid memory region

To read or to write information beyond the allocated memory. The memory you accessed isn't yours, so anything can happen.

```c
void main() {
    int *p = malloc(10 * sizeof(int));
    p[10] = 42; // I wrote on the 11th position! UB!
}
```

Reasonably easy to happen when using arrays.

In Rust, the execution will always be aborted, indicating which code tried to perform an invalid access to memory:

```rust,should_panic
fn main() {
    let x = vec![1, 2, 3];
    // ...
    let v = x[100_000]; // For sure our `vec` doesn't have all that size
    println!("An integer from x: {v}");
}
```

### Manipulate uninitialized memory

Uninitialized memory may contain anything. In the best case, the memory will be zeroed. In the worst, you'll read anything that was written there before.

```c
void main() {
    int *p;
    // ...
    int x = *p; // Dereferencing `p` without initializing it before! UB!
}
```

In Rust, it doesn't even compile:

```rust,compile_fail
fn main() {
    let x: String;
    // Oops, we forgot to initialize...
    println!("{x}");
}
```

## Conclusion

Probably, there's other memory problems that I didn't cover. Fortunately, you'll hardly find them. Yes, it's possible to have UB in Rust if you (or your dependency) uses _unsafe_ incorrectly, but it's very likely that you won't need this language resource to develop your software.

Don't worry about decorating the rules. What is important is to understand what each of them mean. It's the compiler's job to ensure that they are being followed.
