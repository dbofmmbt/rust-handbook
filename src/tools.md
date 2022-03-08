# Ferramentas

A seguir, temos uma lista com algumas das ferramentas que são muito importantes para a linguagem. Cada título de seção possui um link para a documentação da ferramenta em questão, caso você queira informações mais específicas.

## [Rustup](https://rustup.rs)

É o instalador do Rust. Com ele você consegue instalar e atualizar diversos componentes da linguagem. Alguns deles são descritos abaixo.

## [Rustc](https://doc.rust-lang.org/rustc/index.html)

Esse é o compilador da linguagem. Normalmente ele não é utilizado diretamente, pois o Cargo simplifica diversas tarefas no desenvolvimento e lida com o compilador por nós.

## [Cargo](https://doc.rust-lang.org/cargo/index.html)

É o gerenciador de pacotes do Rust. Ele permite:

- Criar um pacote com uma estrutura padronizada: `cargo new <nome_do_projeto>`
- Realizar diversas atividades de desenvolvimento:
  - Checar se o código compila: `cargo check`
  - Rodar testes: `cargo test`
  - Compilar o código: `cargo build`
  - Compilar **e** rodar o código: `cargo run`
- Publicar um pacote para ser usado por outras pessoas: `cargo publish`

Tem muitas outras coisas que podem ser feitas através do cargo, mas esse já é um bom começo.

## [Rustfmt](https://github.com/rust-lang/rustfmt)

É o formatador de código da linguagem. Praticamente todo projeto utiliza ele. Essa padronização diminui trabalho manual ao programar, facilita a entrada em bases de código novas, elimina discussões sobre estilo... As vantagens compensam (e muito) a redução de liberdade no estilo do código.

Para utilizá-lo, basta rodar `cargo fmt` e todo o seu projeto será ajustado.

## [Clippy](https://github.com/rust-lang/rust-clippy)

É o _linter_ da linguagem, que é basicamente uma ferramenta que analisa seu código e é capaz de oferecer sugestões para tentar torná-lo melhor. Principalmente ao começar na linguagem, `cargo clippy` pode te mostrar formas mais idiomáticas na escrita do código, além de identificar erros de programação comuns.

## [Rustdoc](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)

Rust é reconhecida por seu ecossistema ter uma documentação acima da média e isso é fruto de um excelente suporte à geração de documentação através do `rustdoc`. Ao rodar `cargo doc`, a documentação do seu projeto e de suas dependências são construídas e todos têm acesso a uma forma padronizada de documentação, reduzindo o peso da atividade e aumentando a produtividade de quem desenvolve e depende de pacotes de outras pessoas.

## Suporte de IDE

Uma linguagem moderna **precisa** ter um suporte adequado para a interação com o código fonte. Embora Rust seja uma linguagem nova, já existem projetos muito bons para oferecer esse suporte a vários editores.

### [Rust-Analyzer](https://rust-analyzer.github.io/)

É o servidor de linguagem que alimenta vários editores de texto, como VS Code e Vim. No VS Code, basta instalar a extensão `rust-analyzer` para ter o suporte.

### [Intellij Rust](https://www.jetbrains.com/pt-br/rust/)

Para as IDEs baseadas no Intellij, existe um plugin desenvolvido abertamente pela JetBrains que traz suporte a Rust aos seus editores. Basta instalar o plugin e contar com suporte na sua IDE.
