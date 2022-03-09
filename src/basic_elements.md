# Elementos Básicos

Os elementos abaixo, embora simples, são um ponto de partida importante para programar. A maioria desses conceitos estão presentes em outras linguagens.

## Tipos primitivos

Essa seção não é exaustiva por questões de simplicidade. Existem outros tipos primitivos além desses e você pode descobrir sobre todos na [seção de tipos primitivos da documentação da linguagem](https://doc.rust-lang.org/std/index.html#primitives).

### **Inteiros**

- Com sinal: `i8`, `i16`, `i32`, `i64`, `i128` e `isize`. (`i` de _integer_).
  - e.g.: `0`, `-5`, `1_000_000i32`
- Sem sinal: `u8`, `u16`, `u32`, `u64`, `u128` e `usize`. (`u` de _unsigned_, sem sinal).
  - e.g.: `10`, `42`, `1_000_000usize`

O número no tipo representa quantos bits são usados pelo inteiro. Isso tem relação direta com o intervalo de valores que são possíveis de representar. Por exemplo, `u8` consegue representar um intervalo de 2<sup>8</sup> valores.

### **Floats**

`f32` e `f64`. Exemplos: `1.0`, `-15.50f32`

Números flutuantes permitem representar números decimais (com uma imprecisão inerente da representação de ponto flutuante no _hardware_). _Floats_, conforme o padrão IEEE-754, podem ser um pouco mais trabalhosos de lidar do que você estava acostumado em outras linguagens. Rust deixa explícito possíveis tiros no pé que poderiam passar despercebidos.

### **bool**

Pode assumir os valores `true` e `false`.

### Unit

Esse é o tipo do `()`, que é o único valor desse tipo. Ele é usado em locais em que não há um valor significativo definido. Por exemplo: funções que "não retornam nada" na verdade retornam `()`.

## Variáveis

Uma variável guarda um valor e ela pode ser manipulada através de seu identificador. Em Rust, utilizamos o `let` para definir variáveis. `let` trabalha com casamento de padrões, mas por enquanto não iremos tirar proveito desse recurso para manter as coisas simples.

Abaixo segue o esqueleto de declaração e associação (_binding_) de uma variável.

```rust,ignore
// Declaração
let <identificador>: <tipo>;

// Declaração com associação de valor
let <identificador>[: <tipo>] = <expressão>;
```

As variáveis em Rust são imutáveis por padrão. Caso você tente mudar o conteúdo de uma variável imutável, o compilador reclamará:

```rust
// Se você tentar rodar, verá que esse código não compila.
let x = 5;
x = 10;
```

Como nosso querido compilador sugere, basta adicionar `mut` antes do identificador para tornar a variável mutável.

```rust
// Agora vai!
let mut x = 5;
x = 10;
assert_eq!(x, 10);
```

É importante notar que Rust possui inferência de tipos. Isto é, ela tenta identificar tipos não declarados através dos tipos das expressões utilizadas no código. Por exemplo:

```rust,ignore
let is_raining = true;
```

Não é obrigatório anotar o tipo. Já que `true` é do tipo `bool`, `is_raining` também vai ser.

## Operadores

Uma lista completa pode ser encontrada [neste apêndice do livro do Rust](https://doc.rust-lang.org/book/appendix-02-operators.html).

### Aritméticos

`+`, `-`, `/`, `*`, `%`

Caso você não conheça, `%` é o resto de divisão do primeiro número pelo segundo.

Esses operadores podem ser usados em conjunto com atribuição. Por exemplo:

```rust
let mut x = 2 + 2;
x += 1;
assert_eq!(x, 5);
```

### Lógicos

- Negação:`!`
- E lógico: `&&`
- OU lógico: `||`

```rust
let x = true && !false;
assert_eq!(x, true);
```

### Relacionais

`<`, `<=`, `==`, `!=`, `>=`, `>`

```rust
let x = 5 <= 10;
assert_eq!(x, true);

let y = 3 < 1;
assert_eq!(y, false);
```
