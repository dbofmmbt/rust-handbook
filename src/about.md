# Guia para Entender Rust

<!-- TODO inserir imagem -->

Rust é uma linguagem de programação muito bem pensada. Ela busca reunir diversos conceitos desenvolvidos por outras linguagens, indústria e academia de modo a construir uma linguagem sólida para desenvolver soluções eficientes e confiáveis de forma produtiva.

No entanto, ela ganhou uma fama de ser uma linguagem com uma curva de aprendizado alta. Não vou te enganar: Rust possui muitos recursos para te ajudar, mas você precisa entender como eles funcionam antes. O que posso te dizer é que, no geral, cada escolha feita por essa linguagem tem um propósito. Quando você entende os motivos, algo que antes parecia complicado pode se tornar compreensível.

Se você pretende desenvolver uma aplicação com alto grau de processamento concorrente, provavelmente precisará aprender sobre os recursos de programação assíncrona (`async/await`). Caso você lide diretamente com _hardware_ em programação embarcada, talvez precise usar `unsafe` ao interagir com
recursos nativos do dispositivo em questão. Ao desenvolver uma biblioteca, pode parecer interessante implementar macros para criar uma interface mais conveniente.

Saiba que você não precisa aprender tudo de uma vez, nem ser especialista em todos os recursos da linguagem. Cada um pode focar nos recursos que são relevantes para a ferramenta que deseja desenvolver.
Não pense que você precisa absorver tudo de uma vez só, pois ninguém deveria precisar.

O que proponho aqui é o entendimento da linguagem. Existe um núcleo de conceitos os quais toda pessoa desenvolvedora precisa saber para dominá-la, não importa o tipo de aplicação que ela faça. É esse núcleo que busco ensinar nesse curso.

Além disso, vou apresentar esses conceitos de forma pouco usual. Dificilmente você vai encontrar um material que siga essa ordem. Cada tópico é cuidadosamente pensado para que seja embasado nos tópicos anteriores, formando um aprendizado em camadas. Você não tentaria construir o segundo andar de um prédio sem antes construir o primeiro andar, certo?

Qualquer conceito ou detalhamento que eu considere fora do escopo do guia terá referências para conteúdos de alta qualidade, de modo a complementar esse guia.

<!-- TODO gancho para a próxima seção -->
