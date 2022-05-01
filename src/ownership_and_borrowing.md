# Propriedade e Empréstimo

Se você me perguntar: "qual é a coisa mais inovadora de Rust?", minha resposta é _ownership_. Esse conceito permite que a linguagem seja _memory-safe_ sem coletor de lixo. Na prática, você tem o desempenho de uma linguagem de sistemas sem abrir mão da confiabilidade.

Se você veio de linguagens que não sejam C ou C++, isso quer dizer que agora você tem uma linguagem de sistemas que te dá passa a sensação de programar em alto nível. Qualquer um pode escrever código eficiente sem medo de acidentalmente corromper a memória.

Se por acaso você conhece C e C++, você provavelmente já precisou debuggar uma série de erros provenientes do gerenciamento manual de memória. Ficará feliz em saber que o `rustc` simplesmente rejeita programas que podem causar esses erros, com mensagens de erro instrutivas, te explicando o que poderia ocorrer de errado.

## O Conceito

As regras de propriedade são bem simples:

- Todo valor tem uma única variável que é sua dona.
- A propriedade do valor pode ser movida.
- Quando a dona sai de escopo, o valor é liberado.

```rust,editable
fn main() {
    let x = String::from("hello"); // x é dona da string
    // ...
    println!("Inicialmente, x é dona de: {x}");
    // ...
    let y = x; // a string foi movida para `y`. `x` deixou de ser dona.
    // A partir desse ponto, tentar acessar a string através de `x` é uma operação inválida, já que a string foi movida.
    // ...
    // println!("x é dona de: {x}"); // inválido!
    // ...
    println!("Agora, y é dona de: {y}");
    // ...
    // Ao final dessa função, `y` sai de escopo e a string vai ser liberada.
}
```

Além da propriedade, temos o empréstimo:

- O dono de um valor pode emprestá-lo através de referências compartilhadas (imutáveis) ou únicas (mutáveis).
- A qualquer momento, só pode haver uma referência única ou qualquer número de referências compartilhadas.
- Referências são sempre válidas. Elas não podem ser utilizadas após o valor que referenciam ser movido ou liberado.

É isso. Ao respeitar essas regras, estamos livres de qualquer problema envolvendo alocação e liberação de recursos. **E a melhor parte**: o compilador checa se estamos respeitando essas regras automaticamente.

Ilustrando com um exemplo:

```rust,editable

struct Book {
    title: String,
}

fn shared(book: &Book) {
    println!("[shared] O título do livro é: {}\n", book.title);
}

fn unique(book: &mut Book) {
    println!("[unique] O título do livro era: {}", book.title);
    book.title.push_str(" parte 2");
    println!("[unique] Agora o título é: {}\n", book.title);
}

fn be_owner(book: Book) {
    println!("[be_owner] Sou dono do livro: {}\n", book.title);
}

fn main() {
    let mut first_owner = Book {
        title: String::from("Como fazer amigos e influenciar pessoas") // recomendo
    };

    // Aqui estamos concedendo um empréstimo compartilhado do livro para a variável `first_borrow`
    let first_borrow = &first_owner;

    // Criamos uma segunda referência compartilhada para passar para a função `shared`
    shared(&first_owner);

    // Tudo bem você fazer um empréstimo mutável...
    unique(&mut first_owner);

    // Mas você não pode mais acessar `first_borrow`!
    // println!("titulo: {}", first_borrow.title);

    // Mas não tem problema você criar uma nova referência compartilhada.
    let second_borrow = &first_owner;

    // A ideia é que se você tem uma referência compartilhada, você não pode acessar o conteúdo dela caso ele possa ter sido alterado (i.e. por uma referência única ou quando o valor é movido de dono)

    // Tudo okay!
    shared(second_borrow);

    // Propriedade movida para um novo dono.
    let second_owner = first_owner;

    // Todas as referências anteriores deixam de ser válidas por conta do movimento. O antigo dono deixa de ser acessível também!
    // shared(second_borrow);
    // unique(&mut first_owner);

    println!("[main] segundo dono: {}\n", second_owner.title);

    be_owner(second_owner);

    // O livro não é mais acessível aqui, já que sua propriedade foi passada para a função `be_owner`. Nesse ponto, o valor já foi liberado. `second_owner` também não é mais acessível.
    // let invalid_borrow = &second_owner;
}
```

Fique à vontade para brincar com esse exemplo. Repare nas mensagens de erro. Elas são bem descritivas e foram feitas para te ajudar a ter uma melhor compreensão do porquê determinada operação é inválida.

Tudo que você viu aqui permeia a linguagem inteiramente. Com o tempo, esses conceitos ficam naturais e você começa a concordar com o _borrow checker_ (parte do `rustc` que checa se você está respeitando as regras) em vez de lutar com ele. Dá até falta desses conceitos quando volto para outras linguagens, pois é mais fácil entender o que está acontecendo no programa.

Talvez algumas pessoas reparem que certas coisas que causam erro no exemplo do `Book` são possíveis com inteiros e _floats_. A razão disso é que esses tipos são baratos de copiar. Então, em vez de mover o valor, ele é copiado. Isso acontece, pois eles implementam a _trait_ `Copy`. Vamos conversar sobre _traits_ mais adiante.

Além disso, eu propositalmente omiti o conceito de _lifetimes_. Eles são necessários para o compilador garantir que as referências são sempre válidas. Me pareceu mais apropriado abordar esse conceito após explicar código genérico, já que o _lifetime_ é uma generalização do tempo de vida de um valor.

## Do que o `rustc` te protege

Os exemplos a seguir são em C, pois ela permite ilustrar bem como você pode causar comportamento indefinido (UB) no seu programa, que é basicamente uma forma elegante de dizer que ele pode, em qualquer execução da sua aplicação, fazer com que ela:

- Aborte a execução
- Funcione normalmente
- Funcione, mas processe valores inválidos

Quando você tem UB, o melhor que pode acontecer é a execução ser abortada assim que a corrupção ocorre. Assim você pode tentar identificar onde ocorreu o uso indevido de memória e corrigir o problema. Entretanto, pode funcionar na sua máquina, mas em produção não. É, bem divertido.

Particularmente, eu só entendi todos os problemas de memória abaixo quando aprendi Rust. Antes eu esbarrava neles sem saber de fato que tipo de erro estava ocorrendo. No meu aprendizado de C, nenhuma referência que usei me ensinou sobre essas classes de erros de forma clara.

### _use after free_

Consiste em usar uma memória que você já liberou para o sistema. Quando uma região de memória deixa de ser sua, não há garantia que você está acessando uma informação válida.

```c
void main() {
    void *p = malloc(500);
    // ...
    free(p);
    *p = 123; // Escrita em uma região de memória liberada! UB!
}
```

No melhor dos casos, você vai tomar um _segmentation fault_. No pior, o conteudo de `*p` vai ser sobrescrito e você vai processar lixo, fazendo a execução dar erro em outra parte do código, ou pior: seu programa vai gerar um _output_ inválido. Perdi duas noites de sono nesse tipo de erro uma vez.

Em Rust, nem compila:

```rust,compile_fail
fn main() {
    let x = String::from("ferris!");
    drop(x); // A propriedade da string foi movida para `drop`
    // ...
    println!("{x}");
}
```

### _double free_

O mesmo endereço de memória é liberado duas vezes.

```c
void main() {
    void *p = malloc(500);
    // ...
    free(p);
    // ...
    free(p); // Mesmo endereço de memória liberado mais de uma vez! UB!
}
```

Parece improvável de ocorrer, mas em uma refatoração pode ser fácil de introduzir.

Em Rust, nem compila:

```rust,compile_fail
fn main() {
    let x = String::from("ferris!");
    drop(x); // A propriedade da string foi movida para `drop`
    // ...
    drop(x); // Não dá pra passar para `drop` (ou qualquer fn) um valor que foi movido.
}
```

### dereferenciar ponteiro nulo

O [erro de 1 bilhão de dólares](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions). Até linguagens de alto nível, com coletor de lixo, cometem.

```c
void main() {
    void *p = malloc(500); // essa função pode retornar NULL caso a memória não tenha sido alocada com sucesso
    // ...
    *p = 123; // Escrita em NULL! UB!
}
```

O lado bom desse erro é que ele normalmente aborta a execução em vez de processar dados inválidos. O lado ruim... essa é uma classe de erros totalmente evitável, mesmo sem _ownership_. Quanto mais cedo erros são corrigidos, menores são os custos. Se suas ferramentas impedem a introdução de certos tipos de defeitos, melhor ainda.

Em Rust... Não existe referência nula. Nós usamos `Option<T>` para representar um valor do tipo `T` que pode estar presente (`Some(value)`) ou não (`None`).

```rust,editable
fn main() {
    let x: Option<String> = Some(String::from("ferris!"));
    // ...
    // Não posso acessar a string diretamente sem antes checar se `x`, que é uma `Option<String>` é `Some(...)` ou `None`.
    match x {
        Some(value) => println!("{value}"),
        None => println!("Nada aqui!"),
    }
}
```

### Vazamento de Memória

Tecnicamente, vazar memória não é considerado UB. Entretanto, isso consome recursos do sistema desnecessariamente.

```c
void main() {
    void *p = malloc(500000000);
    // Usando p...
    // ...
    // Não preciso mais de p, mas não liberei...
    // ...
}
```

Em Rust, a memória será liberada automaticamente quando o dono sai de escopo. Mesmo que vazar memória seja algo seguro de se fazer em Rust (i.e. alguém pode vazar memória explicitamente se quiser/precisar sem usar `unsafe`), a maior parte do tempo ela vai ser liberada por conta do conceito de propriedade.

### Acessar uma região de memória inválida

Ler ou escrever informações fora dos limites da memória que você alocou. A memória que você acessou não é sua, então qualquer coisa pode acontecer.

```c
void main() {
    int *p = malloc(10 * sizeof(int));
    p[10] = 42; // Escrevi na décima primeira posição! UB!
}
```

Bem fácil de ocorrer com o uso de vetores.

Em Rust, a execução sempre vai ser abortada, indicando onde ocorreu o acesso inválido à memória:

```rust,should_panic
fn main() {
    let x = vec![1, 2, 3];
    // ...
    let v = x[100_000]; // Com certeza nosso vetor não tem esse tamanho todo
    println!("Um inteiro de x: {v}");
}
```

### Manipular memória não inicializada

Memória não inicializada pode conter qualquer coisa. No melhor dos casos o conteúdo vai estar zerado. No pior, você vai ler qualquer coisa.

```c
void main() {
    int *p;
    // ...
    int x = *p; // Dereferenciado p sem iniciazá-lo! UB!
}
```

Em Rust, nem compila:

```rust,compile_fail
fn main() {
    let x: String;
    // Oops, esquecemos de inicializar...
    println!("{x}");
}
```

## Conclusão

Provavelmente existem outros problemas de memória que eu não abordei. Felizmente, você dificilmente vai encontrá-los. Sim, é possível ter UB em Rust se você (ou sua dependência) usar _unsafe_ incorretamente, mas é bastante provável que você não vai precisar desse recurso da linguagem.

Não se preocupe em decorar as regras. O importante é entender o que cada uma delas significa. É trabalho do compilador garantir que elas estão sendo respeitadas.
