---
title: Recarregar o arquivo .bashrc no Linux
date: 2022-07-12
tags:
  - "ubuntu"
  - "linux"
  - "terminal"	
categories:
  - "Linux"
---
Essa é uma dica ligeira e muito útil.

Por vezes é necessário modificar o script de personalização de ambiente de usuário .bashrc seja
para modificar a variável PATH, seja para carregar alguma aplicação após o login do usuário.

Após a modificação é necessário efetuar logoff e depois login para carregar novamente o arquivo, mas
para tornar esse logoff desnecessário, podemos carregar este arquivo na sessão atual.
<!--more-->
O problema surge porque não podemos rodar ./.bashrc:
>leandro@leandro:~$ ./.bashrc  
>bash: ./.bashrc: Permissão negada  
>leandro@leandro:~$ .bashrc  
>.bashrc: comando não encontrado  

Para resolvermos isso usamos o comando source:

>leandro@leandro:~$ source .bashrc

Uma forma alternativa é usar . (ponto):

>leandro@leandro:~$ . .bashrc

O comando source não é uma aplicação, mas, um comando interno do bash
>leandro@leandro:~$ type source  
>source é um comando interno do shell  

Por isso não há man page para ele, mas temos uma pequena descrição usando o comando help
>leandro@leandro:~$ **help source**  
>source: source ARQUIVO [ARGUMENTOS]  
>    Executa comandos de um arquivo no shell atual.  
>    
>    Lê e executa comandos de ARQUIVO no shell atual. As entradas em  
>    $PATH são usadas para localizar o diretório contendo ARQUIVO. Se  
>    quaisquer ARGUMENTOS forem fornecidos, eles se tornam parâmetros  
>    posicionais quando ARQUIVO é executado.  
>    
>    Status de saída:  
>    Retorna o status do último comando executado em ARQUIVO; falha se  
>    ARQUIVO não puder ser lido.  

Assim não será necessário fazer logoff e login e podemos alterar a variável PATH e ver o resultado
da modificação da mesma sessão!
