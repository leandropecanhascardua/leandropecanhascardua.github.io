---
title: Procurando (e encontrando) bugs no código fonte sem rodar o aplicativo - Análise estática de código com graudit
date: 2022-08-14
tags: 
  - "linux"
  - "php"
  - "graudit"
categories:
  - "PHP"
---
Quer um jeito certo de encontrar bugs? Analise o código fonte!

Bem, é isso que a análise estática de código pretende fazer procurando por padrões dentro código e avisando quando esses
padrões são encontrados. 

Com o apoio de uma ferramenta como o **graudit** isso pode ser fácil demais da conta!
<!--more-->
Programadores experientes podem encontrar problemas sem nem mesmo digitar o código fonte (e eu já vi um exemplo desses!).

Mas o auxílio de uma ferramenta permite automatizar o processo e diminui o custo da análise estática de código, nome da técnica
que consiste em vasculhar o código fonte em busca de padrões associados a problemas. 

Uma vez encontrado o padrão, é necessário uma análise pois não necessariamente há uma vulnerabilidade. 

Pode ser que haja verificações tendo em conta o risco e haja proteções adequadas. Ao mesmo tempo, só porque um trecho de código não foi
exposto não significa que não haja ou não haverá problemas ali. 

Temos de levar em conta falsos positivos e falsos negativos, mas dentro do tempo que dispomos para nossos projetos temos de fazer o nos dê o melhor retorno em termo de tempo investido.

O **graudit** é um programa desenvolvido em shell script que usa o utilitário **GNU grep** para vasculhar arquivos de código fonte em busca de padrões de código problemático.

A página do projeto está disponível em (http://www.justanotherhacker.com/projects/graudit/) junto com outras boas ferramentas
do desenvolvedor.

Por utilizar como base o Shell Script, as assinaturas são fáceis de entender e customizar, além de permitir a adição de assinaturas de código próprias para estender a funcionalidade da aplicação.

## Instalação

O método mais recomendado de instalação é através do [github do projeto](https://github.com/wireghoul/graudit). Isso permite obter as assinaturas mais atualizadas.

Vou mostrar a instalação apenas para o meu usuário local. Para isso vou usar o diretório **~/.local/bin** presente em uma configuração padrão do Ubuntu.

> leandro@leandro:~$  cd ~/.local/bin/

> leandro@leandro:~/.local/bin$  git clone https://github.com/wireghoul/graudit

Vamos adicionar o caminho da instalação à variável **$PATH** do sistema, assim como a variável **$GRDIR** que contém o caminho para
o banco de dados de assinaturas (e que será útil mais adiante).

> leandro@leandro:~$ nano ~/.bashrc

> PATH=$PATH:/home/leandro/.local/bin/graudit/  
> export GRDIR=/home/leandro/.local/bin/graudit/signatures/  

E atualizar a sessão atual seguindo a [dica](/dicas/recarregar-bashrc/):

> leandro@leandro:~$ source ~/.bashrc 

ou

> leandro@leandro:~$ . ~/.bashrc

Depois disso já podemos testar a instalação.

>leandro@leandro:~$ graudit  
>\===========================================================  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;.__ __&nbsp;&nbsp;_   
> &nbsp; &nbsp;        ___________&nbsp;&nbsp;&nbsp;_&nbsp;_&nbsp;&nbsp;__|_/|__|/  |_   
>&nbsp;&nbsp;         / __\\_`_\\__  \\ |  |  \\/ __| | \\\ &nbsp;__\  
>&nbsp;        / /_/  >  |\\//__\\|  |  / /_/| |&nbsp;||&nbsp;|  
> &nbsp;       \\___  /|_|  (___/___/\_____| |_||_|  
> &nbsp;      /___/ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\\/           
>              grep rough audit - static analysis tool  
>                  v3.4 written by @Wireghoul  
>=================================[justanotherhacker.com]===  
>Usage: graudit [opts] /path/to/scan  
>  
>OPTIONS  
>  -d <dbname> database to use or /path/to/file.db (uses default if not specified)  
>  -A scan unwanted and difficult (ALL) files  
>  -x exclude these files (comma separated list: -x *.js,*.sql)  
>  -i case in-sensitive scan  
>  -c <num> number of lines of context to display, default is 2  
>  
>  -B supress banner  
>  -L vim friendly lines  
>  -b colour blind friendly template  
>  -z supress colors  
>  -Z high contrast colors  
>   
>  -l lists databases available  
>  -v prints version number  
>  -h prints this help screen  
>   


Temos três conjuntos de argumentos para o comando :

* comportamento
* aparência
* informativos

Com os argumentos de aparência, podemos modificar a apresentação do relatório de saída. 

Por exemplo, com a opção **-B** podemos suprimir o banner de apresentação. com a opção **-z** podemos suprimir as cores na
saída, com **-L** podemos deixar a saída com o padrão de cores do vim (editor de texto de linha de 
comando evolução do vi). 

O conjunto de opções informativos fazem isso, mostram informações sobre o programa: **-v** imprime o número da versão,
**-h** imprime a ajuda e a opção **-l** é a mais valiosa, fornece uma listagem do bancos da dados de assinaturas 
disponíveis.

Com essa opção podemos ver quais plataformas de desenvolvimento ou linguagem de programação podemos analisar, ou mesmo que
categoria de vulnerabilidade queremos encontrar:

>leandro@leandro:~/.local/bin$ **graudit -B -l**  
>/home/leandro/.local/bin/graudit/signatures//actionscript.db  
>/home/leandro/.local/bin/graudit/signatures//android.db  
>/home/leandro/.local/bin/graudit/signatures//asp.db  
>/home/leandro/.local/bin/graudit/signatures//c.db  
>/home/leandro/.local/bin/graudit/signatures//cobol.db  
>/home/leandro/.local/bin/graudit/signatures//default.db  
>/home/leandro/.local/bin/graudit/signatures//dotnet.db  
>/home/leandro/.local/bin/graudit/signatures//exec.db  
>/home/leandro/.local/bin/graudit/signatures//fruit.db  
>/home/leandro/.local/bin/graudit/signatures//go.db  
>/home/leandro/.local/bin/graudit/signatures//ios.db  
>/home/leandro/.local/bin/graudit/signatures//java.db  
>/home/leandro/.local/bin/graudit/signatures//js.db  
>/home/leandro/.local/bin/graudit/signatures//nim.db  
>/home/leandro/.local/bin/graudit/signatures//perl.db  
>/home/leandro/.local/bin/graudit/signatures//php.db  
>/home/leandro/.local/bin/graudit/signatures//python.db  
>/home/leandro/.local/bin/graudit/signatures//ruby.db  
>/home/leandro/.local/bin/graudit/signatures//scala.db  
>/home/leandro/.local/bin/graudit/signatures//secrets-b64.db  
>/home/leandro/.local/bin/graudit/signatures//secrets.db  
>/home/leandro/.local/bin/graudit/signatures//spsqli.db  
>/home/leandro/.local/bin/graudit/signatures//sql.db  
>/home/leandro/.local/bin/graudit/signatures//strings.db  
>/home/leandro/.local/bin/graudit/signatures//xss.db  
>/home/leandro/.local/bin/graudit/signatures/actionscript.db  
>/home/leandro/.local/bin/graudit/signatures/android.db  
>/home/leandro/.local/bin/graudit/signatures/asp.db  
>/home/leandro/.local/bin/graudit/signatures/c.db  
>/home/leandro/.local/bin/graudit/signatures/cobol.db  
>/home/leandro/.local/bin/graudit/signatures/default.db  
>/home/leandro/.local/bin/graudit/signatures/dotnet.db  
>/home/leandro/.local/bin/graudit/signatures/exec.db  
>/home/leandro/.local/bin/graudit/signatures/fruit.db  
>/home/leandro/.local/bin/graudit/signatures/go.db  
>/home/leandro/.local/bin/graudit/signatures/ios.db  
>/home/leandro/.local/bin/graudit/signatures/java.db  
>/home/leandro/.local/bin/graudit/signatures/js.db  
>/home/leandro/.local/bin/graudit/signatures/nim.db  
>/home/leandro/.local/bin/graudit/signatures/perl.db  
>/home/leandro/.local/bin/graudit/signatures/php.db  
>/home/leandro/.local/bin/graudit/signatures/python.db  
>/home/leandro/.local/bin/graudit/signatures/ruby.db  
>/home/leandro/.local/bin/graudit/signatures/scala.db  
>/home/leandro/.local/bin/graudit/signatures/secrets-b64.db  
>/home/leandro/.local/bin/graudit/signatures/secrets.db  
>/home/leandro/.local/bin/graudit/signatures/spsqli.db  
>/home/leandro/.local/bin/graudit/signatures/sql.db  
>/home/leandro/.local/bin/graudit/signatures/strings.db  
>/home/leandro/.local/bin/graudit/signatures/xss.db  
>/home/leandro/.local/bin/graudit/misc/ampscript.db  
>/home/leandro/.local/bin/graudit/misc/flatline.db  
>/home/leandro/.local/bin/graudit/misc/qb64.db  
>/home/leandro/.local/bin/graudit/misc/rce.db  
>/home/leandro/.local/bin/graudit/misc/supression.db  
>/home/leandro/.local/bin/graudit/misc/wordpress.db  

Assim, podemos ver o cardápio do sistema: código fonte em C, Microsoft .NET, Go, Java, Javascript,
PHP, Python, além de vulnerabilidades comuns para Wordpress, Cross-Site Scripting(XSS) e Sql entre outros!

O último conjunto de parâmetros são os relacionados ao comportamento do programa em si. Com a opção **-d** podemos
definir o caminho para um banco de dados, lembrando que podemos omitir o caminho completo se tivermos a variável de ambiente **$GRDIR** setada;com a opção **-x** podemos excluir algumas extensões de arquivo do processamento;com a opção -i podemos
definir uma pesquisa sem fazer a diferenciação entre maiúsculas e minúsculas comum nos ambientes
Linux e com a opção **-c** podemos sintetizar ou ampliar o relatório com a contagem do número de linhas a serem mostradas. 

Isso será melhor explicado no exemplo a seguir.

Como exemplo vamos usar o aplicativo Igreja usando no artigo sobre [phploc](/artigos/phploc/).

>leandro@leandro:~$ **cd Downloads/**  
>leandro@leandro:~/Downloads$ **git clone https://github.com/hiltonbruce/Igreja.git**  

Como devemos lembrar o sistema está construído em PHP. Se formos tentados a rodar:

>leandro@leandro:~/Downloads$ graudit -B -d php Igreja/  
>Igreja/painel_direito.php-40-			$recBuscas->buscarecibo();  
>Igreja/painel_direito.php:41:		}elseif (!empty($painelDireito) && file_exists($painelDireito)) {  
>Igreja/painel_direito.php-42-			//Verifica se h� uma chamada a um painel espec�fico  
>##############################################  
>Igreja/painel_direito.php-46-		//In�cio da pend�ncia  
>Igreja/painel_direito.php:47:		$_urlLi_pen="?escolha={$_GET["escolha"]}&bsc_rol={$_GET["bsc_rol"]}";//Montando o Link para ser passada a classe  
>Igreja/painel_direito.php-48-		$query_pen = "SELECT rol,nome,obs FROM membro WHERE OBS<>'' ";  
>##############################################  
>Igreja/painel_direito.php-52-		//Faz os calculos na pagina��o  
>Igreja/painel_direito.php:53:		$sql2_pen = mysql_query ($query_pen) or die (mysql_error());  
>Igreja/painel_direito.php-54-		$total_pen = mysql_num_rows($sql2_pen) ; //Retorna o total de linha na tabela  
>Igreja/painel_direito.php-55-		$paginas_pen = ceil ($total_pen/$nmpp_pen); //Retorna o total de p?ginas  
>Igreja/painel_direito.php:56:    $_GET["pagina1_pen"] = (empty($_GET["pagina1_pen"])) ? 0 : intval($_GET["pagina1_pen"]);  
>Igreja/painel_direito.php:57:		if ($_GET["pagina1_pen"]<1) {  
>Igreja/painel_direito.php:58:			$_GET["pagina1_pen"] = 1;  
>Igreja/painel_direito.php:59:		} elseif ($_GET["pagina1_pen"]>$paginas_pen) {  
>Igreja/painel_direito.php:60:			$_GET["pagina1_pen"] = $paginas_pen;  
>Igreja/painel_direito.php-61-		}  
>Igreja/painel_direito.php:62:		$pagina_pen = $_GET["pagina1_pen"]-1;  
>Igreja/painel_direito.php-63-		if ($pagina_pen<0) {$pagina_pen=0;} //Especifica um valor p vari?vel p?gina caso ela esteja setada  
  

Receberemos um extenso relatório com um um total de 8068 linhas:

> leandro@leandro:~/Downloads$ graudit -B -d php Igreja/| wc -l  
> 8058  

Sem conhecer o funcionamento interno da ferramenta, podemos ser levados a concluir que existem 8058 pontos de possíveis 
vulnerabilidades. Mas não é isso!

Nosso relatório de saída mostrou o conteúdo de arquivos como Igreja/chat/js/jquery.js e Igreja/chat/js/jquery.js
entre outros, ou seja, conteúdo em javascript. 

Isso poluiu muito o relatório de saída e não me interessa em um primeiro momento pois estou procurando apenas por problemas envolvendo o uso da linguagem **PHP**.  

Em um primeiro momento eu vou excluir arquivos de extensão js (vou-me concentrar primeiro no PHP) com a extensão **-x**. 

Outra coisa incômoda é que no relatório temos vários blocos separados por uma linha contendo exclusivamente
tralhas(#). Entender o que significam essas tralhas ajudam a melhorar a construção de parâmetros executar uma busca eficiente.

Cada bloco contém o nome do arquivo onde foi encontrado a assinatura do padrão(o problema) junto
com o número da linha onde foi encontrado. Ao lado é exibido a linha com o padrão em realce.

Além disso, o graudit retorna a linha encontrada junto com a linha anterior e a posterior. Isso permite uma avaliação mais rápida
do potencial de exploração do padrão encontrado(mas adicionou mais 02 linhas ao resultado final!).

Podemos mudar o número de linhas retornadas junto com o padrão usando o parâmetro **-c**. Podemos,
assim, aumentar a quantidade (ou mesmo eliminar!) . O valor padrão para o parâmetro **-c** é 1. 

Assim, se quisermos retornar duas linhas além do padrão para analisar melhor o código poderíamos fazer:

>leandro@leandro:~/Downloads$ **graudit -B -x \*.js -c 2 -d php Igreja**  
>Igreja/painel_direito.php-39-			$recBuscas->mostra();  
>Igreja/painel_direito.php-40-			$recBuscas->buscarecibo();  
>Igreja/painel_direito.php:41:		**}elseif (!empty($painelDireito) && file_exists($painelDireito)) {**  
>Igreja/painel_direito.php-42-			//Verifica se h� uma chamada a um painel espec�fico  
>Igreja/painel_direito.php-43-			require_once $painelDireito;  
>##############################################  
>Igreja/painel_direito.php-45-		echo "</div>";  
>Igreja/painel_direito.php-46-		//In�cio da pend�ncia  
>Igreja/painel_direito.php:47:		**$_urlLi_pen="?escolha={$_GET["escolha"]}&bsc_rol={$_GET["bsc_rol"]}";//Montando o Link para ser passada a classe**  
>Igreja/painel_direito.php-48-		$query_pen = "SELECT rol,nome,obs FROM membro WHERE OBS<>'' ";  
>Igreja/painel_direito.php-49-		$nmpp_pen="40"; //N?mero de mensagens por p?rginas  
>##############################################  
>Igreja/painel_direito.php-51-		$paginacao_pen['link'] = "?"; //Pagina??o na mesma p?gina  
>Igreja/painel_direito.php-52-		//Faz os calculos na pagina��o  
>Igreja/painel_direito.php:53:		**$sql2_pen = mysql_query ($query_pen) or die (mysql_error());**  
>Igreja/painel_direito.php-54-		$total_pen = mysql_num_rows($sql2_pen) ; //Retorna o total de linha na tabela  
>Igreja/painel_direito.php-55-		$paginas_pen = ceil ($total_pen/$nmpp_pen); //Retorna o total de p?ginas  
>Igreja/painel_direito.php:56:    **$_GET["pagina1_pen"] = (empty($_GET["pagina1_pen"])) ? 0 : intval($_GET["pagina1_pen"]);**  
>Igreja/painel_direito.php:57:		**if ($_GET["pagina1_pen"]<1) {**  
>Igreja/painel_direito.php:58:			**$_GET["pagina1_pen"] = 1;**  
>(...)  

Se houver linhas próximas uma das outras no intervalo definido pelo parâmetro **-c**, as linhas são fundidas e mostradas num bloco só, como mostrado acima.

Importante mencionar que o relatório do graudit não necessariamente aponta vulnerabilidades.

Cada bloco do relatório corresponde a um padrão de problema comum. Para saber se é ou não problema,
é necessário analisar o código. 

Pode ser, por exemplo, que o **graudit** aponte um problema de atribuição de variável $_GET mas os valores sejam exaustivamente tratados no código. Em todo caso, serve como um
bom alerta.

## um exemplo de análise

Como exercício vamos analisar nosso último relatório:

O primeiro bloco aponta **file_exists($painelDireito))** como fonte de problema. 

Aparentemente trata-se apenas de uma chamada de função, então precisamos pesquisar por termos como **"file_exists php exploit"** no Google. 

Encontramos, por exemplo, a https://attackerkb.com/topics/VY0BeXIcp1/cve-2006-4481 . Se explorar essa função parecer promissor, pesquisamos mais sobre ela e
vulnerabilidades encontradas. 

Pode acontecer de esta função ser vulnerável apenas em determinadas versões do PHP, neste caso, se o código for nosso
podemos atualizar a versão no servidor. Se não for nosso e pretendemos atacar, procuramos por instalações desse aplicativo que contenham essa versão.

No segundo bloco de código temos o que parece ser a construção de uma url diretamente a partir da variável $_GET.
Se o valor de **$_urlLi_pen** for impresso diretamente na saída html sem tratamento adequado, teremos cross site scripting. Veja que isso é
especulação. Precisamos ver como essa variável será tratada ao longo do script e do programa como um todo.

No terceiro bloco temos quatro linhas unificadas. A função **mysql_query** está depreciada, mas não encontrei
vulnerabilidades relacionadas especificamente à função. Imagino que o alerta se refira apenas a 
atualizar para uma implementação mais moderna. 

As três últimas linhas são mais interessantes: Fazem um tratamento para um índice no array superglobal **$_GET**.  Parecem indicar que mais à frente esse índice será usado e, por isso,
seu valor está sendo checado. Isso é uma prática não recomendada mas, como falei, não indica a priori
uma vulnerabilidade. É, digamos, um ponto a ser melhorado.

Isso sublinha um ponto interessante que eu gostaria de destacar: o **graudit** pode ser visto sob dois
pontos de vista: o de uma equipe de desenvolvimento e o de um atacante ou analista de segurança!

Sob o ponto de vista de equipe de desenvolvimento indica lugares onde o código pode ser aperfeiçoado
e corrigido. Isso aumenta a qualidade!

Sob o ponto de vista de uma equipe de segurança ou um atacante, significa um ponto onde o código 
está obscuro. Assim, seguindo a linha de execução, pode ser encontrada uma brecha ou algum erro que
leve a uma exploração.

## Conclusão

O graudit é uma ferramenta de análise estática de código fonte muito versátil apesar da simplicidade
da sua construção. 

É um script shell que possui um banco de dados de assinaturas para diversas linguagens
de programação.

Neste artigo eu cobri apenas a instalação e um uso bem básico, talvez eu faça outro artigo fazendo uma análise
mais aprofundada.

Ela pode ser usada tanto pela equipe de desenvolvimento como ferramenta de apoio à qualidade quanto
pela equipe de segurança como meio de avaliar o risco de código. 

Pode ser também usado por caçadores de bugs ou atacantes que possuam acesso ao código fonte como ferramenta para encontrar vulnerabilidades
potenciais ou reais. É bom destacar que pentesters em início de carreira podem se destacar encontrando bugs em projetos opensource
e notificando os desenvolvedores ou sites especializados como (https://packetstormsecurity.com/) e (https://www.exploit-db.com/) . Isso pode garantir um pouco de visibilidade.

Eu devo cobrir posteriormente outras ferramentas para análise estática de código, mas eu considero o graudit uma das melhores
devido à sua proposta!

