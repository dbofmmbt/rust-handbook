# Olá, Mundo

É uma tradição, ao aprender uma linguagem de programação nova, a apresentação do `hello, world!` da linguagem, que consiste em exibir uma mensagem no terminal com os dizeres "Olá, Mundo!".

Então vamos lá:

```rust
fn main() {
    println!("Olá, Mundo!");
}
```

Você pode rodar esse código direto do guia. Basta clicar no botão "play" no canto superior direito da caixa que contém o código.

Só com esse pequeno trecho de código já é possível ter contato com vários elementos da linguagem, que vamos ver com detalhes posteriormente. Não se preocupe em entendê-los a fundo agora.

- A função `main` que define o ponto de entrada da execução. É onde o código da sua aplicação começa a rodar.
- Blocos são definidos com um par de `{` e `}`. Algumas linguagens, como Python, usam identação. Outras usam palavras-chave como `begin` e `end`.
- `println!` é uma macro. Posso dizer isso por conta do `!` no final. No momento da compilação, as macros são processadas para geração de código, que é inserido no local onde foram chamadas. Não se preocupe em entender macros agora, só saiba que usaremos algumas antes de uma explicação mais aprofundada.
- `"Olá, Mundo!"`, falando de forma bem simplificada, pode ser entendida como um literal de texto. Em Rust, existem alguns tipos diferentes de texto (e.g. `String`, `str`) e mais para frente vai ficar claro o porquê.
- `println!("Olá, Mundo!");` é uma declaração que chama uma macro passando um texto como argumento e tem como efeito exibir um conteúdo no terminal (com quebra de linha). Declarações em Rust terminam com `;`. Existem também as expressões, que são processadas em tempo de execução e são reduzidas a um valor. Por exemplo: `2 + 2` é reduzido para o valor inteiro `4`.

Acho que essas informações já são um bom começo para um "Hello, world!". Agora sim começamos com o pé direito!
