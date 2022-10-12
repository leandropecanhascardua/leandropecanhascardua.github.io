---
title: Benchmarking de performance de navegador
date: 2022-10-12
tags:
  - "ubuntu"
  - "linux"
  - "Android"
categories:
  - "Linux"
  - "Dicas"
---
Eu tenho um computador com uma configuração modesta e, se isso não fosse suficiente, com sérios problemas no disco rígido. 

Por isso eu preciso aproveitar cada byte da memória RAM. Sendo assim, eu gasto um bom tempo pesquisando sugestões para melhorar a performance do meu equipamento.

Porém, sempre surge um problema: Como ter certeza de que uma configuração é efetiva? Como saber se realmente houve alguma melhoria (e não uma piora)
no desempenho? Para não confiar no subjetivismo da opinião pessoal, precisamos traduzir a performance em um valor e de uma ferramenta
para fazer a medição.
<!--more-->
Por isso eu trouxe como sugestão o Web Basemark, uma ferramenta para medir o desempenho do navegador usando vários testes gráficos e
de sistema e que implementa algumas recomendações de usabilidade para a web além de testar várias funcionalidades.

Disponível online no endereço https://web.basemark.com ele é muito simples e fácil de usar, como pode ser visto abaixo:

![teste](/img/post/dica05/basemark01.png)

Para rodar o teste, basta clicar no imenso botão **Start**. Podemos também obter algumas informações sobre o sistema clicando em
**Device Info** e sobre o tipo de teste em **configuration**.

É importante destacar que, para obtermos um resultado o mais fiel e preciso possível, é necessário que nenhum outro programa ou aplicação
esteja aberto ou rodando ao mesmo tempo. 

Depois de rodarmos o teste, obtemos nosso score:

![teste](/img/post/dica05/basemark02.png)

Temos, assim, um valor que reflete o nível de desempenho do navegador. No meu caso eu fiz o teste usando o navegador Mozila Firefox versão 102.0 no sistema 
operacional Ubuntu 20.04 em um laptop com processador Intel.

Esses dados são importantes, visto que eu posso querer, por exemplo, saber se o Google Chrome seria mais rápido na minha máquina.

Nesse caso, eu posso usar o Basemark novamente, mas desta vez no Google Chrome e comparar a performance dos dois:

![teste](/img/post/dica05/basemark03.png)

Podemos ver assim que além de uma performance pior que aquela que obtivemos no Firefox, o Chrome ainda apresentou algum problema,destacado na mensagem em vermelho.
Isso já é suficiente para concluirmos que o Firefox se deu melhor que seu concorrente.

Pode ser também que eu queira comparar não dois navegadores, mas dois sistemas! Eu posso instalar o Pop OS, Ubuntu, Arch e Fedora e
calcular a performance nos quatro para decidir com qual deles vou ficar, por exemplo.

Ou mesmo decidir entre versões do mesmo sistema, como as versões 20.04, 21.10 ou 22.04 do Ubuntu e decidir se atualizo ou não um sistema instalado.

Uma coisa importante a se notar é que essa medição não é estática, isto é, fixa. Cada vez que rodarmos o Basemark podemos obter um
valor diferente e isso é natural visto que debaixo dos panos o sistema operacional continua ocupado processando informações.

Eu gosto de imaginar uma sessão de análise de performance como o ato de rodar a ferramenta algumas vezes e tirar alguns valores 
estatísticos como média, mediana, mínimo, máximo e desvio padrão. E usar esses números para fazer a comparação.

Ainda podemos comparar o resultado que obtivemos com várias configurações disponíveis no mercado (outros usuários)clicando no botão "see more results from Power Board".

Dessa forma somos conduzidos até o site https://powerboard.basemark.com onde de fato podemos comparar nossa performance com a de outros sistemas de forma bastante ampla.

Agora, será que podemos melhorar essa pontuação fazendo alterações na configuração do navegador? E na configuração do sistema operacional? Eu pretendo responder
a essa pergunta em um próximo artigo.


