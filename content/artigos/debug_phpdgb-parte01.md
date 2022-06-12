---
title: Debugando aplicações PHP usando phpdbg - parte 01
date: 2022-06-11
tags:
  - "ubuntu"
  - "linux"
  - "PHP"
  - "PHPDBG"
categories:
  - "Linux"
  - "PHP"
---
O PHP é uma linguagem voltada para a web e vem daí sua força, desde sua criação. O que é sua força, também é sua fraqueza e a depuração de erros pode se tornar
uma experiência difícil para os iniciantes.

O PHP tem ferramentas para auxiliar o programador a encontrar e corrigir os erros, entre eles o Xdebug e o phpdbg. 
<!--more-->
O phpdbg é um debugador interativo de linha de comando para explorar e corrigir códigos PHP. 

Ele é rápido, poderoso e fácil de usar(com as limitações de qualquer ferramenta de linha de comando em termos de tempo de aprendizado). Ele possui os seguintes
aspectos:
- debugação linha a linha
- breakpoints flexíveis
- controle da execução do código
- configurável por arquivo
- suporte a debugação remota
- criação de script de debugação.

Além do modo interativo, que é o objetivo deste artigo, o phpdbg também permite a criação de scripts de debugação, que talvez venhamos a explorar em um outro artigo.

# 1. Instalação

Para instalar o phpdbg, precisamos compilar o código fonte com o da mesma versão instalada no sistema. Isso já é garantido pela versão disponível no repositório de
pacotes de cada distribuição e com o Ubuntu não é diferente. Isso deve ser lembrado principalmente quando compilando a partir do código fonte!

Primeiro precisamos ver qual versão temos instalada no nosso sistema:
 >php -v  

A seguir instalamos o pacote phpdbg com a versão correspondente do PHP:

 > sudo apt install php7.4-phpdbg  

# 2. código de Exemplo

Este é o nosso código fonte que usaremos como exemplo:

>leandro@casa:~/Leandro/linux$ cat -n teste.php   
>     1	<?php  
>     2	  
>     3		$arr = [[3, 1.5], [5, 1.2], [10, 5], [2,1.1]];  
>     4	  
>     5		$total=0;  
>     6		foreach($arr as $val){  
>     7			$total = $val[0] * $val[1];  
>     8		}  
>     9	  
>    10		echo("total=$total\n");  
>    11	  
>    12	?>  
>  

Rodando a aplicação pela linha de comando, teríamos:
>leandro@casa:~/Leandro/linux$ php teste.php   
>total=2.2  

Esse resultado está incorreto. Esperaríamos um valor de 3\*1.5 + 5\*1.2 + 10\*5 + 2*1.1 = 62.7
Vamos descobrir onde está o erro!

# 3. Sessão de debugação

O modo básico de iniciar a debugação usando o phpdbg é chamá-lo sem argumentos pela linha de comando:

>leandro@casa:~/Leandro/linux$ phpdbg teste.php   
>[Welcome to phpdbg, the interactive PHP debugger, v7.4.3]  
>To get help using phpdbg type "help" and press enter  
>[Please report bugs to <http://bugs.php.net/report.php>]  
>[Successful compilation of /home/leandro/Leandro/linux/teste.php]  
>prompt>

Isso iniciar um prompt interativo. Para encerrar a sessão de debugação, sair e voltar para a linha de comando do terminal basta digitar **quit**. 

No phpdbg temos três grupos de comandos: 
- **Informação**: São relacionandos ao código fonte e ao opcode. Servem para consultar qual a linha de código está sendo executada, qual o opcode foi gerado
a partir do código fonte e como está sendo executado, além de mostrar o valor de variáveis
.
- **Controle de execução**: Permite controlar a sessão de debugação, isto é, configurar um ponto de parada, iniciar a execução, voltar a executar o código depois de uma
parada, vigiar uma variável ou encerrar a execução entre outras opções.

- **Diversos**: Não têm uma característica comum, mas permitem entre outras coisas configurar o ambiente phpdbg, rodar um script phpdbg, rodar um comando no shell entre
outras opções.

Digitando **help** no menu interativo do phpdbg podemos ver a lista completa dos comandos permitidos e digitando help nome do comando podemos ver detalhes de um comando específico. 

O modo mais básico é rodar o comando **run** do menu interativo(**help run** para mais detalhes). Ele vai executar o código até o final ou até que um breakpoint seja encontrado. Nosso código não tem breakpoints ainda, então a execução irá até o fim.

>leandro@casa:~/Leandro/linux$ phpdbg teste.php   
>[Welcome to phpdbg, the interactive PHP debugger, v7.4.3]  
>To get help using phpdbg type "help" and press enter  
>[Please report bugs to <http://bugs.php.net/report.php>]  
>[Successful compilation of /home/leandro/Leandro/linux/teste.php]  
>prompt> run  
>total=62.7  
>[Script ended normally]  
>prompt>  

Como podemos ver o resultado foi o mesmo obtido executando o script pela linha de comando. Podemos passar um argumento para o comando **run**, que será entendido
como o argumento passado para o script na linha de comando , ou seja, seu argumento $argv (não usarei neste artigo).

Podemos ver o código fonte que estamos depurando usando o comando **list**. Podemos passar argumentos para esse comando, para sinalizar o que estamos querendo ver exatamente. Podemos ver linhas de código, funções, métodos ou uma classe inteira. Rode **help list** no menu interativo para mais detalhes. Nosso código não
tem funções, por isso vamos analizar linhas de código. Para ver o código completo, podemos rodar:

>leandro@casa:~/Leandro/linux$ phpdbg teste.php   
>[Welcome to phpdbg, the interactive PHP debugger, v7.4.3]  
>To get help using phpdbg type "help" and press enter  
>[Please report bugs to <http://bugs.php.net/report.php>]  
>[Successful compilation of /home/leandro/Leandro/linux/teste.php]  
>prompt> list l 20  
> 00001: <?php  
> 00002:   
> 00003: 	$arr = [[3, 1.5], [5, 1.2], [10, 5], [2,1.1]];  
> 00004:   
> 00005: 	$total=0;  
> 00006: 	foreach($arr as $val){  
> 00007: 		$total = $val[0] * $val[1];  
> 00008: 	}  
> 00009:   
> 00010: 	echo("total=$total\n");  
> 00011:   
> 00012: ?>  
> 00013:   
>prompt>  


Veja que passamos o parâmetro 20, indicando que queremos ver 20 linhas, mas o código só tem 13 linhas. Sem problemas! 

A quantidade de linhas se conta a partir da execução corrente, ou seja, em qual linha o código parou. Como o código não está em execução, a contagem é a partir do começo, se
estivéssemos parados em um breakpoint, a contagem seria a partir da linha do breakpoint ou da linha que tivesse sendo executada por último, se usarmos a função **step**.

Agora vamos tentar corrigir nosso código. Como o valor total está incorreto, nossa suspeita é a de que a linha 7 ($total = $val[0] * $val[1];) está com problema. Vamos tentar descobrir qual é. 

Essa linha está dentro de um loop foreach. Nosso plano, então, é setar um breakpoint na linha 7, iniciar a execução, esperar a interrupção e analisar o valor de $val[0], $val[1] e $total. Veremos se os valores do array $arr estão corretos e se a expressão completa está correta.

>prompt> break 7  
>[Breakpoint #0 added at /home/leandro/Leandro/linux/teste.php:7]  
>prompt> run  
>[Breakpoint #0 at /home/leandro/Leandro/linux/teste.php:7, hits: 1]  
> 00007: 		$total = $val[0] * $val[1];  
> 00008: 	}  
> 00009:   

Nosso código rodou e parou na linha 7. Isso significa que a linha 7 não foi executada ainda. 

Podemos visualizar o valor das variáveis com o comando **ev** no prompt (**help ev** para mais detalhes)o. Vamos analizar o valor de $val, $val[0], $val[1] e $total:

>prompt> ev $val  
>Array  
>(  
>    [0] => 3  
>    [1] => 1.5  
>)  
>prompt> ev $val[0]  
>3  
>prompt> ev $val[1]  
>1.5  
>prompt> ev $total  
>0  
>prompt>  

Podemos ver que os valores estão corretos, ou seja, não há erro de atribuição incorreta. São aqueles que nós estávamos esperando. Agora queremos executar o loop para analisar novamente essa linha. 

Para isso, usamos o comando **continue** (**help continue** para mais detalhes) no prompt interativo. Poderíamos ter a situação de um bloco de código dentro do foreach com mais de um breakpoint. 
Se esse fosse o caso para o nosso codigo, a execução prosseguiria para esse novo breakpoint, até retornar para o início do bloco e para novamente em nosso breakpoint.

Podemos também rodar o código linha a linha através do comando **step** no menu interativo. Isso é mais útil em códigos complexos em que não sabemos exatamente onde
pode estar o problema. Num artigo posterior faremos um exemplo com este comando. 

Prosseguindo, temos:

> prompt> continue  
>[Breakpoint #0 at /home/leandro/Leandro/linux/teste.php:7, hits: 2]  
> 00007: 		$total = $val[0] * $val[1];  
> 00008: 	}  
> 00009:   
>prompt> ev $val  
>Array  
>(  
>    [0] => 5  
>    [1] => 1.2  
>)  
>  
>prompt> ev $val[0]  
>5  
>prompt> ev $val[1]  
>1.2  
>prompt> ev $total  
>4.5  
>prompt>   

Podemos ver que até aqui tudo está certo. Executemos mais uma vez então:

> prompt> continue  
>[Breakpoint #0 at /home/leandro/Leandro/linux/teste.php:7, hits: 3]  
> 00007: 		$total = $val[0] * $val[1];  
> 00008: 	}  
> 00009:   
>prompt> ev $val  
> Array  
>(  
>    [0] => 10  
>    [1] => 5  
>)  
>  
>prompt> ev $val[0]  
>10  
>prompt> ev $val[1]  
>5  
>prompt> ev $total  
>6  
>prompt>   

Aqui podemos ver que há um problema com a variável $total. Na execução anterior o valor era 4.5 e agora o valor é 6 e não 10.5! Ou seja, o valor da variável não
está sendo incrementado no loop! De fato, podemos ver no código donte que isso não está acontecendo. Precisamos mudar a linha 7 de 
> $total = $val[0] * $val[1];

para 

> $total += $val[0] * $val[1];

Podemos encerrar a sessão atual com o comando **quit**, alterar o código fonte e carregar novamente o phpdbg. Mas se quisermos, podemos remover os breakpoints e 
rodar o código até o final, mas o código em execução ainda não conterá a modificação:

>prompt> clear  
>Clearing Breakpoints  
>File              1  
>Functions         0  
>Methods           0  
>Oplines           0  
>File oplines      0  
>Function oplines  0  
>Method oplines    0  
>Conditionals      0  
>prompt> continue  
>total=2.2  
>[Script ended normally]  
>prompt>   


Depois de efetuar a modificação, rodamos o código pela linha de comando e podemos verificar que a correção foi suficiente:
>leandro@casa:~/Leandro/linux$ php teste.php   
>total=62.7  

Assim nossa tarefa está concluída!

# 4. Conclusão
Vimos neste artigo como realizar uma sessão de debugação ou depuração de código usando o phpdbg pela linha de comando no Ubuntu Linux 20.04

Esse é um recurso muito menosprezado e até mesmo desconhecido no desenvolvimento PHP. Muitos são os programadores que possuem anos de experiência e nem mesmo
sabem que há recursos eficientes para remoção de erros nesta maravilhosa linguagem. 

Exploramos o debugador de linha de comando phpdbg. Existem outros, como o XDebug que se adaptam melhor ao ambiente web e fornecem recursos mais amplos, mais adiante
teremos um artigo sobre ele.

Mais adiante exploraremos também recursos mais avançados do phpdbg. 

E aí? É melhor usar o phpdbg ou colocar echo/var_dump no código e refresh na página?

