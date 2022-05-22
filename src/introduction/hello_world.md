# Hello, World

It's a tradition, when starting to learn a new programming language, to present a program which consists of showing a `hello, world!` message on the terminal.

Here we go:

```rust
fn main() {
    println!("Hello, World!");
}
```

You can run this code straight from the book. Just click in the "play" button in the top-right corner of the box which contains the code.

Just with this little piece of code it's possible to get in touch with a lot of elements of the language, which we'll see with details later. Don't worry trying to understand them deeply now.

- The `main` function defines the execution's entry point. That's where your application's code starts to run.
- Blocks are defined with a pair of `{` and `}`. Some languages, such as Python, use indentation. Others use keywords such as `begin` and `end`.
- `println!` is a macro. I can say that because of the `!` in the end. When your program is compiled, macros are processed for code generation, which is inserted where they've been called. Don't worry about understanding macros now, just know that we'll use some of them before a deeper explanation.
- `"Hello, World!"`, in a simplified view, can be seen as a text literal. In Rust, there are a few different types of text (e.g. `String`, `str`), each with its use case. It turns out that strings are more complicated than most programmers think, and Rust chose to be explicit about it.
- `println!("Hello, World!");` is a statement which calls a macro passing a text as argument and has the effect of showing a content in the terminal (with a line break in the end). Statements in Rust end with `;`. There are expressions too, which are processed at runtime and reduced to a value. For example: `2 + 2` is reduced to the integer value `4`.

I think that these information are a good start for a "hello, world!". Now we're starting on the right foot!
