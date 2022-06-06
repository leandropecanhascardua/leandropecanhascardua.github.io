---
title: Habilitando a compilação JIT (Just In Time) para o PHP8 no Ubuntu 
date: 2022-06-05
tags:
  - "ubuntu"
  - "linux"
  - "php"	
categories:
  - "PHP"
  - "Dicas"
---
O recurso Just In Time foi adicionado à versão 8 do PHP lançada em 2020 e promete ganhos interessantes de performance. 

Esse recurso pode ser entendido como uma evolução do OPCache (que já existe) e que permite guardar
um trecho de código já processado em memória compartilhada, eliminando alguns passos de processamento para melhorar a performance. 

O Just In time, porém, é um passo além: Ele permite guardar um trecho já processado diretamente código binário nativo para a máquina, eliminando momentaneamente o interpretador para turbinar a aplicação!

<!-- more -->
É claro que não são todas as aplicações que vão se beneficiar deste recurso, mas isso já uma outra história.

Na instalação padrão do PHP esse recurso vem desabilitado (Até o momento da escrita deste artigo). Então vamos verificar como está a configuração do ambiente primeiro. 

## Verificar se JIT está habilitado

Primeiramente, vamos criar um script auxiliar para explorar as configurações do ambiente. Abra seu editor de textos favorito e digite o seguinte código:
<?php
	phpinfo();
?>

Salve com o nome teste.php

Nosso teste será com o servidor interno do PHP executado pela linha de comando, mas os passos serão os mesmos para o Apache, o que mudará será apenas o arquivo de configuração php.ini.

Vamos então rodar o servidor na porta 9000 na mesma pasta onde está o arquivo teste.php:

>$ php -S localhost:9000


Agora verificamos a configuração acessando http://localhost:9000/teste.php no navegador:

![Verificando a configuração](/img/post/dica01/jit_01_mod.png)

Podemos ver que estamos usando o arquivo /etc/php/8.0/cli/php.ini  olhando o item "Loaded Configuration File". É esse arquivo que vamos modificar.

Agora vamos verificar se o recurso JIT está ou não habilitado. Descemos a página até encontrar o item "JIT" na seção "Zend OPcache"

![Verificando a configuração](/img/post/dica01/jit_02_mod.png)

Como podemos ver, está desabilitado

## Modificando o arquivo de configuração php.ini

Primeiramente vamos verificar algumas configurações relacionadas ao opcache:

>leandro@leandro:~$ grep zend_extension /etc/php/8.0/apache2/php.ini  
>;zend_extension=opcache  

>leandro@leandro:~$ grep opcache.enable /etc/php/8.0/apache2/php.ini  
>;opcache.enable=1  
>;opcache.enable_cli=1  
>;opcache.enable_file_override=0  

Como podemos ver as linhas estão comentadas, o que significa que o recurso está desabilitado. Pode ser que a linha opcache.enable esteja com valor (opcache.enable=0). Neste caso modifique para o valor 1 e salve o conteúdo.

Precisamos adicionar as seguintes linhas ao final do arquivo:
>opcache.jit_buffer_size=100M  
>opcache.jit=1235  
>opcache.jit_debug=1  

Pode acontecer de o recurso JIT não ser habilitado por causa de incompatibilidade com outro plugin ou extensão. Veríamos uma mensagem como a seguinte:
>leandro@leandro:/tmp$ php -S localhost:9000  
>Cannot load Zend OPcache - it was already loaded  
>[Sun Jun  5 23:06:44 2022] **PHP Warning:  JIT is incompatible with third party extensions that override zend_execute_ex(). JIT disabled. in Unknown on line 0**  
><br />  
><b>Warning</b>:  JIT is incompatible with third party extensions that override zend_execute_ex(). JIT disabled. in <b>Unknown</b> on line <b>0</b><br />  
>[Sun Jun  5 23:06:44 2022] PHP 8.0.17 Development Server (http://localhost:9000) started  

No meu caso, a incompatibilidade foi com a extensão XDebug, usada durante o desenvolvimento para finalidades de depuração. Será necessário desabilitar essa extensão. 
Normalmente as extensões são instaladas num diretório conf.d junto ao arquivo php.ini. No meu caso seria /etc/php/8.0/cli/conf.d/
Para facilitar já temos um arquivo com o nome da extensão:

>leandro@leandro:~$ cat  /etc/php/8.0/cli/conf.d/20-xdebug.ini  
>zend_extension=xdebug.so  
>xdebug.mode = debug  

Como podemos ver, a extensão está habilitada. Vamos desabilitar apenas setando o valor Off

>leandro@leandro:~$ cat  /etc/php/8.0/cli/conf.d/20-xdebug.ini  
>zend_extension=xdebug.so  
>;xdebug.mode = debug  
>xdebug.mode=Off  

A partir daqui o recurso já está configurado e operacional se todas as extensões incompatíveis estiverem desabilitadas. 

Para testar, precisamos reiniciar o nosso servidor de linha comando (rodar novamente) e se estivermos configurando o Apache precisamos reiniciar o servidor

>sudo systemctl restart apache2

Agora acessamos novamente http://localhost:9000/teste.php para verificar o status:

![Verificando a configuração](/img/post/dica01/jit_03_mod.png)

## Conclusão:

Embora a nova versão do PHP tenha trazido novidades interessantes para o quesito performance,pelo menos no momento atual elas não vêm configuradas por padrão, sendo necessário proceder à modificação dos arquivos do sistema.

Eu usei nesta dica o servidor interno do PHP disponível pela linha de comando, mas as modificações serão as mesmas para o servidor Apache (não testei usando NGinx ou outros servidores http).



