---
title: Debugando aplicações PHP usando phpdbg - parte 02
date: 2022-07-02
tags:
  - "ubuntu"
  - "linux"
  - "PHP"
  - "PHPDBG"
categories:
  - "Linux"
  - "PHP"
---
Embora possamos criar aplicações de linha de comando em PHP, o ambiente mais comum é em
aplicações web.

Assim, precisamos usar ou simular um contexto web para explorar nosso código. É isso o que
faremos agora.
<!--more-->
Na primeira parte deste artigo, que pode ser visto [aqui](/artigos/debug_phpdgb-parte01/) vimos
os comandos iniciais do **phpdbg**. Agora é hora de aumentar nosso conhecimento.

Tomemos o código de exemplo abaixo, típicamente web:
```php
<?php //teste.php
        if($_POST){
                $nome  = $_POST['nome'];
                $senha = $_POST['senha'];

                echo("Nome: $nome <br>");
                echo("Senha: $senha <br>");
                die();
        }
?>
<html>
<body>
        <form action="" method="post">
                <p>Nome:<input type="text" name="nome" /></p>
                <p>Senha:<input type="password" name="senha" /></p>
                <input type="submit" value="Enviar" />
        </form>
</body>
</html>
```

Há dois campos de textos em um formulário e quando o usuário clica em enviar, os dados são
submetidos via http para o servidor para processamento. A saída pode ser vista abaixo:

![resultado do script](/img/post/art02/phpdbg01.png)

Se rodarmos esse código dentro do debugador, como fizemos no artigo anterior, teríamos:
>leandro@leandro:/tmp/teste$ phpdbg teste.php   
>[Welcome to phpdbg, the interactive PHP debugger, v8.0.20]  
>To get help using phpdbg type "help" and press enter  
>[Please report bugs to <http://bugs.php.net/report.php>]  
>[Successful compilation of /tmp/teste/teste.php]  
>prompt> r  
>\<html>  
>\<body>  
>&emsp;	\<form action="" method="post">  
>&emsp; &emsp;	\<p>Nome:<input type="text" name="nome" /></p>  
>&emsp; &emsp;	\<p>Senha:<input type="password" name="senha" /></p>  
>&emsp; &emsp;	\<input type="submit" value="Enviar" />  
>&emsp;	\</form>  
>\</body>  
>\</html>  
>[Script ended normally]  
>prompt>  

Como podemos ver o debugador mostrou a saída corretamente, mas como vamos passar os dados
dos campos do formulário?

É simples, vamos passar como variável por script usando o comando **ev**(help ev, para mais detalhes) do phpdbg.

A variável $_POST é um array associativo super global, assim como **$_GET**, **$SESSION** entre outros.

![resultado do script](/img/post/art02/phpdbg02.png)

Para saber qual o conteúdo que devemos passar, podemos usar a função interna var_dump do PHP, como mostrado
abaixo.

A saída será mostrada abaixo com o conteúdo que nos interessa destacado.

![resultado do vardump](/img/post/art02/phpdbg03.png)

Podemos ver que $_POST é um array associativo. Agora podemos rodar nosso script dentro do phpdbg setando o valor apropriado para $_POST

> $_POST= ["nome"=> "leandro", "senha"=> "leandro"];

Assim nossa sessão de debugação será:
>leandro@leandro:/tmp/teste$ phpdbg teste.php   
>[Welcome to phpdbg, the interactive PHP debugger, v8.0.20]  
>To get help using phpdbg type "help" and press enter  
>[Please report bugs to <http://bugs.php.net/report.php>]  
>[Successful compilation of /tmp/teste/teste.php]  
>prompt> **ev $_POST= ["nome"=> "leandro", "senha"=> "leandro"];**    
>Array  
>(  
>    [nome] => leandro  
>    [senha] => leandro  
>)  
>     
>prompt> r  
>Nome: leandro <br>Senha: leandro <br>  
>[Script ended normally]  
>prompt>   

Perfeito!!! Mas...

Em cada sessão vamos precisar digitar o valor de $_POST, o que significa se não salvarmos
em algum lugar será extremamente desgastante passar o valor de $POST a todo momento. 

Podemos melhorar? Sim! Podemos! Então vamos ver!

Salvamos o contéudo da variável $POST em um arquivo como script PHP e vamos importar em
nosso código. Digamos que eu tenha salvo o arquivo debug01.php com o conteúdo abaixo:

```php
<?php //debug01.php
        $_POST= ["nome"=> "leandro", "senha"=> "leandro"];
?>
```
Criamos um script que apenas seta o valor de uma variável. Como vamos usá-la no phpdbg?

Vamos usar o comando include do PHP e o comando **ev** do phpdbg.

>leandro@leandro:/tmp/teste$ phpdbg teste.php   
>[Welcome to phpdbg, the interactive PHP debugger, v8.0.20]  
>To get help using phpdbg type "help" and press enter  
>[Please report bugs to <http://bugs.php.net/report.php>]  
>[Successful compilation of /tmp/teste/teste.php]  
>prompt> **ev include("debug01.php")**  
>1  
>prompt> r  
>Nome: leandro <br>Senha: leandro <br>  
>[Script ended normally]  
>prompt>  

Como podemos ver isso facilitou nossa vida e flexibilizou bastante nossa sessão de debugação.

Agora podemos criar vários arquivos e em cada um deles um valor diferente para a variável $_POST, 
estabelecendo, assim, vários perfis, correspondendo a diversos cenários de debugação.

Ficou com dúvida em alguma coisa? Releia o primeiro artigo:

[Debugando aplicações PHP usando phpdbg - parte 01](/artigos/debug_phpdgb-parte01/)

