# Tools

Next, there's a list of a few tools which are very important for the language. Each section title has a link to that tool's documentation, in case you want more specific information.

## [Rustup](https://rustup.rs)

Rust's installer. With it you can install and update many language tools. Some of them are described below.

## [Rustc](https://doc.rust-lang.org/rustc/index.html)

Language's compiler. Usually, it's not used directly, because `cargo` simplifies some development-related tasks and deals with the compiler for us.

## [Cargo](https://doc.rust-lang.org/cargo/index.html)

Rust's build tool and package manager. It allows to:

- Create a package with a standardized structure: `cargo new <project_name>`
- Perform some development activities:
  - Check if the code compiles: `cargo check`
  - Run tests: `cargo test`
  - Compile the code: `cargo build`
  - Compiled **and** run the code: `cargo run`
- Publish a package to be used by other people: `cargo publish`

There's many other things which can be done using `cargo`, but that's a good start.

## [Rustfmt](https://github.com/rust-lang/rustfmt)

Rust's code formatter. Virtually every project uses it. This standardization reduces repetitive work while coding, facilitates the onboarding on new code bases, ends discussions about style... The advantages compensate (by a lot) the freedom's reduction on the code style choice.

To use it, just run `cargo fmt` and all your project will be formatted.

## [Clippy](https://github.com/rust-lang/rust-clippy)

Language's linter, which is basically a tool which analyzes your code and is capable of offering suggestions to try to make it better. Specially in the beginning, `cargo clippy` can show you more idiomatic ways to write code. Besides that, it can also identify some common programming mistakes.

## [Rustdoc](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)

Rust is known by its ecosystem having above-average documentation and this is due to the excellent documentation generation support we have from `rustdoc`. By running `cargo doc`, the documentation from your project and your dependencies are built and everyone has access to them in an standardized way, reducing the weight of this activity and improving the productivity of those who write or read documentation, which is basically everyone.

## IDE Support

A modern language **needs** to have good support to interact with source code. Although Rust is a new language, there are very good projects to offer this support to code editors.

### [Rust-Analyzer](https://rust-analyzer.github.io/)

It's the Language Server which powers many text editors, like VS Code and Vim. On VS Code, just need to install the `rust-lang.rust-analyzer` extension to have great support.

### [Intellij Rust](https://www.jetbrains.com/pt-br/rust/)

For Intellij-based IDEs, there's a plugin openly developed by JetBrains which brings Rust support for their editors. Just go to the plugins manager and install it to use Rust on Intellij.
