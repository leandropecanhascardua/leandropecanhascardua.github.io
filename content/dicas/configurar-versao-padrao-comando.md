---
title: Configurando uma versão padrão para comandos no Ubuntu Linux 20.04
date: 2022-06-15
tags:
  - "ubuntu"
  - "linux"
  - "terminal"	
categories:
  - "Linux"
  - "Dicas"
---
A necessidade desta dica veio de um problema: O ubuntu instala por padrão duas versões do Python: a versão 2.7 e a versão 3.8. 

À medida em que o python é a base de construção dos ambientes gráficos, pode tornar-se confuso 
gerenciar a versão padrão a ser usada quando chamamos o interpretador na linha de comando. Mas esse problema atinge outros utilitários do sistema.
<!--more-->

Se buscarmos pelas versões do python na pasta /usr/bin, temos :

>leandro@leandro:~$ **ls /usr/bin/python\* -l**  
>lrwxrwxrwx 1 root root       9 mar 13  2020 /usr/bin/python2 -> python2.7  
>-rwxr-xr-x 1 root root 3674216 mar  8  2021 /usr/bin/python2.7  
>lrwxrwxrwx 1 root root       9 mar  7 15:25 /usr/bin/python3 -> python3.8  
>-rwxr-xr-x 1 root root 5490448 mar 15 09:22 /usr/bin/python3.8  
>lrwxrwxrwx 1 root root      33 mar 15 09:22 /usr/bin/python3.8-config -> x86_64-linux-gnu-python3.8-config  
>lrwxrwxrwx 1 root root      16 mar 13  2020 /usr/bin/python3-config -> python3.8-config  

Isso significa que para executar um script programa.py eu tenho que rodar

> python2.7 programa.py

ou

> python3.8 programa.py

Na verdade, como pudemos ver na primeira listagem, python2 é um link simbólico para python2.7 assim como python3 é um link simbólico para python3.8. Nenhum problema.

Mas por vezes pegamos scripts que definem, por exemplo:

#!/usr/bin/env python

Não referenciando explicitamente a versão a ser usada(esperando uma versão padrão). Neste caso o programa não roda e precisamos alterar o código fonte e explicitar qual versão usaremos.

Podemos resolver rapidamente criando um link simbólico. Assim como na primeira listagem python2 aponta para python2.7, podemos ter um
link python apontando para python2.

Podemos inclusive implementar essa ideia diretamente rodando:

>leandro@leandro:~$ **sudo ln /usr/bin/python2 /usr/bin/python**  
>[sudo] senha para leandro:   
>leandro@leandro:~$ **python**  
>Python 2.7.18 (default, Mar  8 2021, 13:02:45)   
>[GCC 9.3.0] on linux2  
>Type "help", "copyright", "credits" or "license" for more information.  
>\>\>\>  

O problema dessa solução é que se tivermos muitas versões de um programa, pode se tornar complicado fazer esse gerenciamento, dado que envolve manipular diretamente os arquivos e torna a operação sujeita a erros.

Por isso é melhor usar o **update-alternatives** para gerenciar as versões do programa que temos instalado na máquina.

O que o update-alternatives faz é gerenciar as versões disponíveis de cada comando e fornecer uma interface mais simples para o gerenciamento. Com esse comando os links são guardados no diretório **/etc/alternatives** e estes 
apontam para os comandos reais. 

Isso permite manter não apenas versões de comandos, mas opções de configuração também. Um exemplo é o comando editor. Esse comando é apenas um item de configuração que aponta para um dos editores de linha de comando disponíveis 
no sistema. Para o meu sistema, as opções são estas:

>leandro@leandro:/etc/alternatives$ **update-alternatives --list editor**  
>/bin/ed  
>/bin/nano  
>/usr/bin/vim.tiny  

Isso torna o sistema mais flexível ao separar um item do comando que o executa.

Então, voltando ao nosso pequeno problema com o python, rejeitando a solução de criar um link simbólico manualmente, vamos adicionar uma chave de configuração para o python com a opção --install:

>leandro@leandro:~$ **sudo update-alternatives --install /usr/bin/python  python /usr/bin/python2.7 1**  
>update-alternatives: a usar /usr/bin/python2.7 para disponibilizar /usr/bin/python (python) em modo auto  

O comando --install recebe os argumentos <link> <nome> <caminho> <prioridade> que são, respectivamente: o nome padrão do comando como será chamado no terminal, o nome dele a ser gerenciado pelo update-alternatives, o caminho para o
item real, ou a versão do item que queremos gerenciar e uma prioridade, que indicam uma preferência. Para cada versão a ser configurada esse comando com a opção **--install** deve ser executado.

Agora checaremos nossa configuração:

>leandro@leandro:~$ **update-alternatives --list python**  
>/usr/bin/python2.7  

Adicionando a versão 3 do python para o controle:

>leandro@leandro:~$ sudo update-alternatives --install /usr/bin/python  python /usr/bin/python3.8 2  
>update-alternatives: a usar /usr/bin/python3.8 para disponibilizar /usr/bin/python (python) em modo auto  

Checando tudo:

>leandro@leandro:~$ **update-alternatives --list python**  
>/usr/bin/python2.7  
>/usr/bin/python3.8  

Agora podemos modificar a versão padrão do python que usaremos em nosso sistema executando:

>leandro@leandro:~$ **sudo update-alternatives --config python**  
>Existem 2 escolhas para a alternativa python (disponibiliza /usr/bin/python).  
>  
>Selecção   Caminho             Prioridade Estado  
>\------------------------------------------------------------  
>\* 0            /usr/bin/python3.8   2         modo automático  
>  1            /usr/bin/python2.7   1         modo manual  
>  2            /usr/bin/python3.8   2         modo manual  
>  
>Pressione <enter> para manter a escolha actual[*], ou digite o número da selecção:   

Podemos ver que a versão padrão selecionada é a primeira (índice 0). Se rodarmos o comando python na linha de comando agora temos:

>leandro@leandro:~$ **python**  
>Python 3.8.10 (default, Mar 15 2022, 12:22:08)   
>[GCC 9.4.0] on linux  
>Type "help", "copyright", "credits" or "license" for more information.  
>\>\>\>   

Para obtermos informações completas passando a opção **--display**:
>leandro@leandro:~$ **update-alternatives --display python**  
>python - modo automático  
>A melhor versão do link é /usr/bin/python3.8  
>  o link actualmente aponta para /usr/bin/python3.8  
>  link python é /usr/bin/python  
>/usr/bin/python2.7 - prioridade 1  
>/usr/bin/python3.8 - prioridade 2  
>leandro@leandro:~$  

Para informações completas consulte a man page

>leandro@leandro:~$ **man update-alternatives**  








