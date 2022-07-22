---
title: Avaliando a qualidade do código PHP com Phploc
date: 2022-07-21
tags: 
  - "linux"
  - "php"
categories:
  - "Linux"
---
Em muitas situações é necessário entrar em contato com código desconhecido, desenvolvido por outra pessoa.

Como etapa preliminar, pode ser útil obter estatísticas que informem algo sobre o código , 
seja como medida de qualidade ou como informação da estruturação do código.

Há várias ferramentas que calculam métricas para um sistema ou aplicação. Para
projetos desenvolvidos em PHP vamos usar a ferramenta mais simples que existe: o phploc
<!--more-->

Antes de analisarmos um relatório da ferramenta, é bom termos em mente que os valores
são estatísticas, ou seja, são tiradas de um conjunto, representam um conjunto. Isso significa que 
pode ser que haja uma parte do sistema feita de uma forma e outra parte desenvolvida de outra forma(o 
que é comum em equipes com alta rotatividade de desenvolvedores).

Por isso, sem o contexto do código você pode interpretar da forma como quiser. Isso significa que
apenas os números não são suficientes para dizer a qualidade de um projeto, mas é uma boa dica!

O Phploc fornece bons indicadores da qualidade geral do código, por isso consegue dar uma boa visão da
qualidade estrutural de um projeto. De novo, um sistema pode ser desenvolvido por diversas equipes e
um módulo estar melhor escrito que outro. Ainda assim nosso exemplo abaixo nos indicará alguns 
pontos a serem observados mesmo sem abrirmos o código fonte.

O **Phploc** fornece métricas para código procedural(o PHP clássico) ou código orientado a objetos. Por isso
algumas estatísticas podem ou não fazer sentido dependendo do paradigma utilizado (ou não utilizado!).

Assim, por exemplo, muitas chamadas de métodos estáticos ou muitas classes abstratas podem indicar um problema 
de desenho(projeto). Ou até mesmo uso de poucas classes, que pode indicar um "código mistureba", em que o desen-
vedor utiliza o paradigma procedural mas de vez em quando usa uma classe para prover um recurso 
específico.

Tendo em vista que a orientação a objetos foi sendo adicionada ao PHP de forma incremental, isso acontece bastante!

# Instalação

Pode ser que o repositório de pacotes do sistema tenha o phploc disponível. Se tiver, poderíamos instalar executando no DEBIAN
ou derivados:

> leandro@leandro:~$ sudo apt install phploc

Contudo, eu prefiro instalar baixando o arquivo phploc.phar. Para mim deu menos problemas durante a execução:

>leandro@leandro:~/Downloads$ wget https://phar.phpunit.de/phploc.phar  
>leandro@leandro:~/Downloads$ cp phploc.phar ~/.local/bin/phploc  
>leandro@leandro:~/Downloads$ chmod +x ~/.local/bin/phploc  

Devemos lembrar de editar a variável PATH para que ela encontre o binário. Para isso, adicione 

> PATH=$PATH:~/.local/bin/ 

no final do arquivo **~/.bashrc** e fazemos logoff e login novamente para termos o acesso configurado em
nosso ambiente, ou também podemos rodar: 

> source ~/.bashrc

como vimos em minha [dica sobre como recarregar  arquivo .bashrc](/dicas/recarregar-bashrc/). E assim
testar a chamada:

>leandro@leandro:~/Downloads$ phploc  
>phploc 7.0.2 by Sebastian Bergmann.  
>  
>No directory specified  

# Uso básico

Para aprendermos a usar o **phploc** vamos baixar um projeto do github para servir de exemplo.

Eu vou usar como exemplo um sistema para controle de igrejas que pode ser baixado [aqui](https://github.com/hiltonbruce/Igreja), ou via git:

>leandro@leandro:~/Downloads$ git clone https://github.com/hiltonbruce/Igreja.git

Nós podemos passar como argumento para o phploc um diretório ou um arquivo. Se passarmos um diretório,
o relatório será sobre todos os arquivos dentro dele enquanto um arquivo como argumento somente vai
gerar relatório sobre ele. 

Isso é útil, por exemplo, quando temos um script alvo para refatoração.

Então, examinando nosso projeto de exemplo, o que obteríamos?

>leandro@leandro:~/Downloads$ **phploc Igreja/**  
>phploc 7.0.2 by Sebastian Bergmann.  
>  
>Directories                                         40  
>Files                                              487  
>  
>**Size**  
>  Lines of Code (LOC)                            48803  
>  Comment Lines of Code (CLOC)                    4118 (8.44%)  
>  Non-Comment Lines of Code (NCLOC)              44685 (91.56%)  
>  Logical Lines of Code (LLOC)                   16374 (33.55%)  
>    Classes                                       2916 (17.81%)  
>      Average Class Length                          33  
>        Minimum Class Length                         1  
>        Maximum Class Length                       337  
>      Average Method Length                         10  
>        Minimum Method Length                        0  
>        Maximum Method Length                      110  
>      Average Methods Per Class                      3  
>        Minimum Methods Per Class                    1  
>        Maximum Methods Per Class                   17  
>    Functions                                     1513 (9.24%)  
>      Average Function Length                       12  
>    Not in classes or functions                  11945 (72.95%)  
>  
>**Cyclomatic Complexity**  
>  Average Complexity per LLOC                     0.28  
>  Average Complexity per Class                   11.60  
>    Minimum Class Complexity                      1.00  
>    Maximum Class Complexity                     90.00  
>  Average Complexity per Method                   4.22  
>    Minimum Method Complexity                     1.00  
>    Maximum Method Complexity                    49.00  
>  
>**Dependencies**  
>  Global Accesses                                 2798  
>    Global Constants                                49 (1.75%)  
>    Global Variables                                30 (1.07%)  
>    Super-Global Variables                        2719 (97.18%)  
>  Attribute Accesses                              1298  
>    Non-Static                                    1298 (100.00%)  
>    Static                                           0 (0.00%)  
>  Method Calls                                    2405  
>    Non-Static                                    2391 (99.42%)  
>    Static                                          14 (0.58%)  
>  
>**Structure**  
>  Namespaces                                         0  
>  Interfaces                                         0  
>  Traits                                             0  
>  Classes                                           86  
>    Abstract Classes                                 0 (0.00%)  
>    Concrete Classes                                86 (100.00%)  
>      Final Classes                                  0 (0.00%)  
>      Non-Final Classes                             86 (100.00%)  
>  Methods                                          283  
>    Scope  
>      Non-Static Methods                           283 (100.00%)  
>      Static Methods                                 0 (0.00%)  
>    Visibility  
>      Public Methods                               283 (100.00%)  
>      Protected Methods                              0 (0.00%)  
>      Private Methods                                0 (0.00%)  
>  Functions                                        117  
>    Named Functions                                117 (100.00%)  
>    Anonymous Functions                              0 (0.00%)  
>  Constants                                         24  
>    Global Constants                                24 (100.00%)  
>    Class Constants                                  0 (0.00%)  
>      Public Constants                               0 (0.00%)  
>      Non-Public Constants                           0 (0.00%)  
   
O relatório é dividido em quatro seções, além de um pequeno resumo da quantidade de arquivos e diretórios:

> Directories                                         40  
> Files                                              487  

O **phploc** conta apenas arquivos com extensão **php**. As seções do relatório final são:

* **Size**: Calcula várias métricas relacionadas à quantidade de linhas de código. Embora não seja
comum aplicar no dia a dia, existem vários estudos que usam a quantidade de linhas de código para
estimar tempo de desenvolvimento, custo de projeto e quantidade de defeitos entre outros.

* **Cyclomatic Complexity**: Calcula a complexidade do código, o número de pontos de decisão dentro
de uma classe ou trecho de código e que está relacionada à testabilidade do código e à dificuldade
de compreensão do funcionamento interno.

* **Dependencies**:Calcula, vamos dizer, o acoplamento interno do código, ou seja, o quanto uma alteração em
um trecho de código pode provocar problemas em outro. Isso acontece por meio de variáveis globais ou
métodos estáticos.

* **Structure**:Calcula métricas relacionadas ao paradigma de desenvolvimento: orientado a objetos ou
procedual.

Vamos analisar em detalhe cada seção:
## Size
Como mencionado anteriormente, calcula estatísticas relacionadas à quantidade de linhas de código:

>**Size**  
>&nbsp;&nbsp;**Lines of Code (LOC)**                           48803  
>&nbsp;&nbsp;**Comment Lines of Code (CLOC)**                    4118 (8.44%)  
>&nbsp;&nbsp;**Non-Comment Lines of Code (NCLOC)**              44685 (91.56%)  
>&nbsp;&nbsp;**Logical Lines of Code (LLOC)**                   16374 (33.55%)  

Temos 48,803 linhas de código, dos quais 16,374 são linhas lógicas de código, ou seja, contém
código fonte(não é linha em branco nem comentário). É importante mencionar que há diversos modos de calcular linhas de código.

Neste [link](https://www.aivosto.com/project/help/pm-loc.html) você pode ler algumas definições.

Podemos ver que a taxa de comentários é de 8,44% o que é uma boa taxa analisando apenas o valor numérico.
É claro que precisamos entrar nos meandros do código para avaliar a qualidade real, mas é um início 
confortador. 


A seguir temos algumas estatísticas de linhas de código relacionadas ao desenvolvimento orientado a
objetos. Como regra temos a contagem da média, do menor e do maior. É sempre bom ficar de olho na relação
entre esses três valores. Um valor mínimo ou máximo muito distante da média pode representar um problema.
Valor mínimo igual a zero é um problema e deve ser investigado.

>**Classes**                                       2916 (17.81%)  
>&nbsp; **Average Class Length**                          33  
>&nbsp;&nbsp; **Minimum Class Length**                         1  
>&nbsp;&nbsp; **Maximum Class Length**                       337  

Existem 2916 linhas de código dentro de classes no sistema, cerca de 17,81%, o que parece baixo. 

Isso pode significar que o sistema tem uma parte desenvolvida com orientação a objetos e outra parte desenvolvida no paradigma procedural.

O tamanho médio das classes é de 33 linhas de código, sendo que existe uma classe que tem apenas uma linha! Será necessário
investigar por que isso está acontecendo!

Podemos listar os arquivos do projeto com **find**(as listagens serão cortadas-são trechos da listagem total):

>leandro@leandro:~/Downloads$ **find Igreja  -name '*.php'**   
>Igreja/painel_direito.php  
>Igreja/bk_nv_convertido/nv_convetid.php  
>Igreja/content.php  
>Igreja/top_igreja.php  
>Igreja/tesouraria/prestacao.php  
> ...

Para vermos os arquivos junto com a quantidade de linhas de cada arquivo .php podemos rodar:

>leandro@leandro:~/Downloads$ **find Igreja  -name '*.php' -exec wc -l {} \;|sort -n**  
>0 Igreja/help/vazio.php  
>0 Igreja/tab_auxiliar/ramais.php  
>1 Igreja/tesouraria/compras.php  
>1 Igreja/tesouraria/contratos.php  
>1 Igreja/tesouraria/fornecedores.php  
>1 Igreja/tesouraria/index.php  
>1 Igreja/tesouraria/no_index.php  
>3 Igreja/adm/igreja.php  
>\...  
>456 Igreja/relatorio/bkp_ficha.php  
>595 Igreja/models/agenda.class.php  
>642 Igreja/views/login.php  
>1140 Igreja/relatorio/formcadastro.php  
>1169 Igreja/relatorio/formcadastroBKP.php  
>1213 Igreja/func_class/classes.php  
>1364 Igreja/func_class/funcoes.php  

Podemos ver que temos arquivos sem nada dentro e arquivos com apenas uma linha de código. Além disso
parece que o autor seguiu uma convenção de adicionar a string **class** ao nome dos arquivos que definem classes.

Neste caso para obtermos apenas os arquivos que contenham classes e sua respectiva quantidade de linhas podemos fazer

>leandro@leandro:~/Downloads$ **find Igreja  -name '*.php' -exec wc -l {} \;|sort -n | grep class**  
>14 Igreja/models/selfonte.class.php  
>14 Igreja/models/setor.class.php  
>17 Igreja/models/formbairro.class.php  
>17 Igreja/models/incluir.class.php  
>18 Igreja/models/setores.class.php  
>\...


Ainda dentro das estatísticas de código orientado a objetos. Temos o comprimento de métodos.

>**Average Method Length**                         10  
>**Minimum Method Length**                        0  
>**Maximum Method Length**                      110  

Veja que temos um método sem código dentro e pelo menos um método com comprimento dez vezes maior que
a média. Isso deve ser investigado (mas não necessariamente representa um problema)

>**Average Methods Per Class**                      3  
>**Minimum Methods Per Class**                    1  
>**Maximum Methods Per Class**                   17  

Além disso temos a métrica de quantidade de métodos por classe. Em média as classes têm três métodos,
mas existe pelo menos uma classe com 17 métodos mas isso não parece muito problemático.

Por fim temos métricas relacionandas a funções, que são o cerne do desenvolvimento procedural.

>**Functions**                                     1513 (9.24%)  
>**Average Function Length**                       12  

Temos 1513 linha de código dentro de funções, cerca de 9,24%. Temos, por fim, a quantidade de código que não 
está dentro de classes nem de funções - é código de escopo global:

>**Not in classes or functions**                  11945 (72.95%)  

Esse valor parece elevado, então talvez o sistema não faça uso de um framework mvc nem use mecanismos de orientação a objetos
para realizar o processamento das requisições que chegam(**Rotas/Controller**).

Isso é uma hipótese apenas e será necessário entrar no código para saber mais.

## Cyclomatic Complexity

Basicamente a complexidade ciclomática mede a quantidade de caminhos independentes dentro de um programa.
Isso indica o quão fácil é compreender o funcionamento de um módulo ou trecho de código e, portanto, o
quão arriscado é alterar algo.

Indica, também, a quantidade de testes que seriam necessários para verificar o seu funcionamento, no mínimo
01 teste para cada caminho, embora possamos ter mais testes para verificar os valores limítrofes das
condições.

Essencialmente essa métrica é calculada a partir da quantidade de comandos de condição existentes no
sistema (IF, THEN, ELSE, SWITCH/CASE) mas também pode levar em conta comandos de tratamento de exceções
(TRY/CATCH). 

Por isso não existe um modo padrão de calcular e diferentes ferramentas podem apresentar
valores diferentes para essa métrica e isso é absolutamente normal.

Uma tabela relacionando complexidade ciclomática e risco pode ser vista [aqui](https://support.scitools.com/support/solutions/articles/70000582297-understanding-mccabe-cyclomatic-complexity).

O primeiro valor desta seção representa a complexidade média por linha de código lógica. 

Há outros indicadores semelhantes, com fórmula de cálculo ligeiramente diferente. 

>&nbsp;**Average Complexity per LLOC**                     0.28

Para avaliarmos esse indicador, precisamos de outras aplicações de domínio semelhante, o que é bem difícil. Eu não encontrei
uma tabela de referência para ajudar a compreender o que esse indicador pode informar.

Esse valor é metade do que encontrei em outro projeto que estava analisando (que tinha muito menos linhas de código - isso diz mais sobre o outro projeto do que sobre este), 
é tudo que posso dizer! 

Temos o cálculo da métrica para desenvolvimento orientado a objetos. Temos uma complexidade média de 
11, porém um máximo de 90. Então temos pelo menos uma classe que pode ser mais difícil manter. 

Talvez tenha muita lógica, é necessário investigar. Pode ser um candidato para refatoração.

>**Average Complexity per Class**                   11.60  
>&nbsp;**Minimum Class Complexity**                     1.00  
>&nbsp;**Maximum Class Complexity**                     90.00  

Em seguida temos a mesma estatística para métodos.
>**Average Complexity per Method**                   4.22  
>&nbsp;**Minimum Method Complexity**                     1.00  
>&nbsp;**Maximum Method Complexity**                    49.00  

Podemos ver uma média de 4,22, porém um valor máximo de 49. Pode ser que haja um método sobrecarregado, que
precise de refatoração. Talvez este método pertença à classe vista acima e seja o responsável pela
sobrecarga de complexidade. É necessário investigar.

Um problema do phploc é que ele não diz o valor da complexidade de cada classe! Sabemos que pode haver problemas, mas não 
sabemos por enquanto onde! A mesma coisa para métodos!

Pode ser necessário usar outra ferramenta para ter esses valores ou então construirmos nossa própria, visto que o cômputo
é simples.

## Dependencies

Estas métricas fornecem o grau de acoplamento interno dentro do sistema.

>**Global Accesses**                                 2798  
>&nbsp;**Global Constants**                                49 (1.75%)  
>&nbsp;**Global Variables**                                30 (1.07%)  
>&nbsp;**Super-Global Variables**                        2719 (97.18%)  

Temos uma quantidade elevada de acessos globais (2798), sendo que quase todo esse acesso é feito 
através de variáveis super globais($_POST, $_GET, $_SESSION etc). 

Isso pode indicar que valores passados pelo cliente são atribuídas diretamente a variáveis dentro do sistema. Se isso acontece, pode representar
problemas de segurança, mas, de novo, é necessário uma investigação para saber a razão deste número.

Como estatística, a métrica sintetiza um conjunto. Parece que a lógica interna está muito dependente e
outro aspecto que isso pode provocar é uma baixa automação de testes. 

Ou seja, após cada modificação o sistema deve ser inteiramente testado "na mão".

Podemos usar novamente a linha de comando do Linux para encontrar quais arquivos são mais problemáticos e
candidatos a uma refatoração.
 
>leandro@leandro:~/Downloads$ **find Igreja  -name '*.php' -exec grep -Hc -i  -e '$_POST' -e 'S_GET' {} \;**  
>Igreja/painel_direito.php:0  
>Igreja/bk_nv_convertido/nv_convetid.php:0  
>Igreja/content.php:1  
>Igreja/top_igreja.php:0  
>Igreja/tesouraria/prestacao.php:0  
>Igreja/tesouraria/pesq_recibo.php:0  
>Igreja/tesouraria/no_index.php:0  
>Igreja/tesouraria/envelope.php:0  
>Igreja/tesouraria/agenda.php:32  
>\...

O comando acima buscou apenas **$_POST** e **$_GET**, mas serve de base para posterior aperfeiçoamento.

Na parte orientada à objetos do sistema, não há acesso de atributos estáticos diretamente, o que é um
bom sinal.

>&nbsp;**Attribute Accesses**                              1298  
>&nbsp;&nbsp;**Non-Static**                                    1298 (100.00%)  
>&nbsp;&nbsp;**Static**                                           0 (0.00%)  

E há 14 chamadas de métodos estáticos, representando 0,58% do total. Parece um valor baixo, mas só 
podemos ter certeza se isso é ou não um problema examinando o código fonte.

>&nbsp;**Method Calls**                                    2405  
>&nbsp;&nbsp;**Non-Static**                                    2391 (99.42%)  
>&nbsp;&nbsp;**Static**                                          14 (0.58%)  

## Structure

Esta seção computa métricas relacionadas ao projeto, como o sistema foi construído (paradigma utilizado).

>**Structure**  
>&nbsp;&nbsp;**Namespaces**                                         0  
>&nbsp;&nbsp;**Interfaces**                                         0  
>&nbsp;&nbsp;**Traits**                                             0  

A ausência de interfaces indica baixa abstração. As classes estão muito acopladas, conectadas. A modificação 
em uma pode gerar problema em outra. 

A ausência de namespace indica que pode haver baixa
organização, visto que todas as classes estão em um único escopo global. De novo, analisando o código
descobriremos se isso é um problema ou não.

>&nbsp;**Classes**                                           86  
>&nbsp;&nbsp;**Abstract Classes**                                 0 (0.00%)  
>&nbsp;&nbsp;**Concrete Classes**                                86 (100.00%)  
>&nbsp;&nbsp;**Final Classes**                                  0 (0.00%)  
>&nbsp;&nbsp;**Non-Final Classes**                             86 (100.00%)  

Ainda nas métricas relacionadas ao desenvolvimento orientado a objetos, temos as estatísticas
relacionadas aos métodos dentro das classes.

Há 86 classes no sistema, todas concretas. Não existe classe abstrata o que, de novo, pode indicar
baixa abstração e alto acoplamento. 

Pode indicar falta de experiência com os conceitos do desenvolvimento
orientado a objetos,mas, de novo, só um indicador e, mesmo que haja, pode não representar um problema.

Além disso, temos 63 arquivos de definição de classe. Então há arquivos que definem mais de uma
classe ou então arquivos que definem classes mas que não tem a string **class** no nome (isso poderia
significar uma quebra no padrão de nomenclatura):

>leandro@leandro:~/Downloads$  find Igreja/  -name '*class.php'| wc -l   
>63  


>**Methods**                                          283  
>&nbsp;    **Scope**  
>&nbsp;&nbsp;**Non-Static Methods**                           283 (100.00%)  
>&nbsp;&nbsp;**Static Methods**                                 0 (0.00%)  
>&nbsp;**Visibility**  
>&nbsp;&nbsp;**Public Methods**                               283 (100.00%)  
>&nbsp;&nbsp;**Protected Methods**                              0 (0.00%)  
>&nbsp;&nbsp;**Private Methods**                                0 (0.00%)  

Todos os métodos são não estáticos e todos os métodos são públicos. 

O **phploc** não calcula, mas a ausência de métodos protegidos indica falta de herança. 

A falta de métodos estáticos pode indicar que variáveis do ambiente do sistema como dados de acesso
são tratados por meio de constantes ou variáveis superglobais. Mas isso é só uma especulação.

Depois das métricas OO, temos métricas para funções(paradigma procedural):

>**Functions**                                        117  
>&nbsp;&nbsp;**Named Functions**                                117 (100.00%)  
>&nbsp;&nbsp;**Anonymous Functions**                              0 (0.00%)  

**Named Functions** são as funções normais. Existem também funções sem nome(**anonymous functions**) e
**arrow functions**, que são nomenclaturas ainda mais sintéticas para funções, usualmente para
criação de funções que serão usadas apenas uma vez, por exemplo, para serem passadas como parâmetro
para outras funções.

Todas as funções são nomeadas. Isso pode indicar que não houve necessidade de criação de funções
que fossem utilizadas apenas uma vez, mas também pode indicar falta de experiência com os recursos
do PHP pela equipe de desenvolvimento.

Por fim temos as informações sobre constantes:
 
>**Constants**                                         24  
>&nbsp;&nbsp;**Global Constants**                                24 (100.00%)  
>&nbsp;&nbsp;**Class Constants**                                  0 (0.00%)  
>&nbsp;&nbsp;**Public Constants**                               0 (0.00%)  
>&nbsp;&nbsp;**Non-Public Constants**                           0 (0.00%)  

Temos 24 constantes, todas de escopo global.

## Para mais além

Além de permitir uma fotografia instantânea de um projeto em PHP, o **phploc** pode ser usado ao longo
de um projeto em desenvolvimento para acompanhar sua evolução.

Quando usado desta forma, A quantidade de linhas de código (ou outra métrica) pode ser usada como
estimador de tempo e também como ferramenta de avaliação da qualidade e da necessidade de refatoração ao longo do tempo.

Podemos salvar o conteúdo do relatório em formato **csv**, **html** ou **xml** com as respectivas opções **--log-csv**,
 **--log-json** ou **--log-xml**. Por exemplo:

>leandro@leandro:~/Downloads$ **phploc --log-csv 21072022.csv   Igreja/**  
>phploc 7.0.2 by Sebastian Bergmann.  
>  
>Directories                                         40  
>Files                                              487  
>(...)  

Analisando o conteúdo do arquivo 21072022.csv:

>leandro@leandro:~/Downloads$ **cat 21072022.csv**   
>Directories,Files,Lines of Code (LOC),Cyclomatic Complexity / Lines of Code,Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC),LLOC outside functions or classes,Namespaces,Interfaces,Traits,Classes,Abstract Classes,Concrete Classes,Final Classes,Non-Final Classes,Classes Length (LLOC),Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods,Protected Methods,Private Methods,Cyclomatic Complexity / Number of Classes,Cyclomatic Complexity / Number of Methods,Functions,Named Functions,Anonymous Functions,Functions Length (LLOC),Average Function Length (LLOC),Average Class Length,Average Method Length,Average Methods per Class,Constants,Global Constants,Class Constants,Public Class Constants,Non-Public Class Constants,Attribute Accesses,Non-Static Attribute Accesses,Static Attribute Accesses,Method Calls,Non-Static Method Calls,Static Method Calls,Global Accesses,Global Variable Accesses,Super-Global Variable Accesses,Global Constant Accesses,Test Classes,Test Methods  
>"40","487","48803","0.27574203004764","4118","44685","16374","11945","0","0","0","86","0","86","0","86","2916","283","283","0","283","0","0","0","11.604651162791","4.2226148409894","117","117","0","1513","12.931623931624","33.906976744186","10","3.2906976744186","24","24","0","0","0","1298","1298","0","2405","2391","14","2798","30","2719","49","0","0"  

O resultado é exibido na tela e salvo em disco.

Com base nisso, podemos criar um job no CRON e salvar um relatório toda semana. Por exemplo, vamos
criar um script **relatorio_phploc.sh**

``` bash
#!/bin/bash

# configurar o caminho até o executável do phploc, pois o cron roda com uma variável PATH diferente daquela do usuário. 
# Necessário passar um caminho absoluto
PHPLOC=/home/leandro/.local/bin/phploc

# passar o caminho completo até a pasta do projeto
PASTA_PROJETO=/home/leandro/Downloads/Igreja

# passar o caminho completo até onde o arquivo de relatório será salvo.
# O formato abaixo será "dia""mês""ano""hora""minuto".csv, 
# por exemplo: 210720221852.csv. Rode man date no terminal para ver mais opções
# se desejar modificar
ARQUIVO_RELATORIO=/home/leandro/Downloads/$(date +%d%m%Y%H%M).csv

# linha de comando. O phploc salva em arquivo e exibe o resultado na saída padrão. 
# Como a saída padrão não nos interessa, descarte esse resultado em /dev/null
$PHPLOC  --log-csv=$ARQUIVO_RELATORIO   $PASTA_PROJETO  1> /dev/null
```
Na pasta onde salvarmos esse script devemos habilitá-lo para execução com:

> leandro@leandro:~/Downloads$ chmod +x relatorio_phploc.sh

Podemos adicionar uma regra ao CRON rodando:

> leandro@leandro:~$ **crontab -e**

Esse comando vai abrir um editor de texto, onde podemos adicionar o comando para executar nosso
script relatorio_phploc.sh todo sábado ao meio dia(na pasta do usuário leandro rodando como usuário
leandro):

> 0 12 * * 6 leandro /home/leandro/Downloads/relatorio_phploc.sh

essa linha deve ser adicionada no final do arquivo aberto pelo crontab -e. Se o editor for o **NANO**
aperte control+x para salvar e sair.

Os logs do **CRON** são salvos em **/var/log/syslog** (pelo menos nas variantes do DEBIAN). Toda vez que o 
script for executado vai aparecer uma linha de log como esta:

> Jul 21 19:05:01 leandro CRON[16001]: (leandro) CMD (~/Downloads/relatorio_phploc.sh

Portanto, para saber quando o comando foi (ou não) executado, basta fazer:

>leandro@leandro:~/Downloads$ **grep CMD  /var/log/syslog**

# Conclusão

O phploc é uma excelente ferramenta para dar uma visão inicial de um projeto. 

Ele fornece métricas de desenvolvimento orientado a objetos e de paradigma procedural.

Contudo, por fornecer estatísticas, os números precisam ser avaliados junto com alguma pesquisa de 
forma a não fornecer dados incorretos. Por exemplo, o suporte a orientação a objetos foi sendo 
adicionado ao PHP desde a versão 5 de forma incremental. 

Por isso, em códigos mais antigos pode ser
que não encontremos classes ou métodos, mas isso não significa um desenvolvimento pobre, significa
que não era comum usar esse paradigma na época em que o sistema foi feito. Então o sistema não fez
uso de orientação a objetos por um motivo que não conhecemos de antemão!

A ferramenta tem um pequeno problema de não mostrar as métricas por arquivo, mas isso devido à sua proposta
de ser simples de usar e de entender. Mas podemos contornar esse problema com recursos de scripting do shell linux
ou mesmo usar outra ferramenta para isso.

Além de uma visão inicial de um projeto de terceiros, ele também permite gerar relatório em formato
csv, xml e json, o que nos permite criar um agendador para coletar métricas de tempos em tempos e
assim acompanharmos a evolução de um projeto interno.
