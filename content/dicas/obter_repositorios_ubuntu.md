---
title: Obter repositórios pela linha de comando no Ubuntu ou pelos derivados Debian
date: 2022-07-30
tags:
  - "ubuntu"
  - "linux"

categories:
  - "Linux"
  - "Dicas"
---
Distribuições Linux derivadas do Debian como Ubuntu, Linux Mint entre outras usam o gerenciador de pacotes APT para fornecerem
um método cômodo e eficaz de instalar aplicativos sem a necessidade de o usuário se preocupar com dependência entre pacotes.

Contudo, o advento de novos releases e versões pode gerar um amontoado de repositórios que o iniciantes podem sentir-se tentados 
a misturar para instalar alguma aplicação específica. Principalmente devido a dicas de Internet antigas.

Quando é necessário obter ajuda, é necessário fornecer a lista completa dos repositórios de pacotes configurados na máquina.
<!--more-->
Quando esses problemas acontecem, a solução consiste em desabilitar os repositórios "alienígenas". Para isso, é importante
obter a listagem dos repositórios. As distribuições normalmente fornecem ferramentas gráficas para lidar com os repositórios, mas
quando é necessário fornecer essa listagem para uma equipe de suporte ou para um fórum de usuários, é necessário
usar a linha de comando para gerar um relatório conciso e completo.

Há dois comandos que podem ser usados com esse propósito:

## 1. APT

É a ferramenta de gestão de pacotes dos sistemas derivados do DEBIAN. Podemos usar o comando **policy** para obter a lista
dos repositórios:

>leandro@leandro:~$ **apt policy**  
>Arquivos de pacote:  
>&nbsp;100 /var/lib/dpkg/status  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release a=now  
>&nbsp;500 http://ppa.launchpad.net/ubuntuhandbook1/avidemux/ubuntu focal/main i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=LP-PPA-ubuntuhandbook1-avidemux,a=focal,n=focal,l=Avidemux,c=main,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin ppa.launchpad.net  
>&nbsp;500 http://ppa.launchpad.net/ubuntuhandbook1/avidemux/ubuntu focal/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=LP-PPA-ubuntuhandbook1-avidemux,a=focal,n=focal,l=Avidemux,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin ppa.launchpad.net  
>&nbsp;500 http://ppa.launchpad.net/ondrej/php/ubuntu focal/main i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=LP-PPA-ondrej-php,a=focal,n=focal,l=***** The main PPA for supported PHP versions with many PECL extensions *****,c=main,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin ppa.launchpad.net  
>&nbsp;500 http://ppa.launchpad.net/ondrej/php/ubuntu focal/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=LP-PPA-ondrej-php,a=focal,n=focal,l=***** The main PPA for supported PHP versions with many PECL extensions *****,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin ppa.launchpad.net  
>&nbsp;500 https://dl.google.com/linux/chrome/deb stable/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=1.0,o=Google LLC,a=stable,n=stable,l=Google,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin dl.google.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/multiverse i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=multiverse,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=multiverse,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/universe i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=universe,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=universe,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/restricted i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=restricted,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=restricted,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/main i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=main,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;500 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-security,n=focal,l=Ubuntu,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin security.ubuntu.com  
>&nbsp;100 http://br.archive.ubuntu.com/ubuntu focal-backports/universe i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-backports,n=focal,l=Ubuntu,c=universe,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;100 http://br.archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-backports,n=focal,l=Ubuntu,c=universe,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;100 http://br.archive.ubuntu.com/ubuntu focal-backports/main i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-backports,n=focal,l=Ubuntu,c=main,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;100 http://br.archive.ubuntu.com/ubuntu focal-backports/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-backports,n=focal,l=Ubuntu,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/multiverse i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=multiverse,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=multiverse,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/universe i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=universe,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=universe,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/restricted i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=restricted,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=restricted,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/main i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=main,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal-updates,n=focal,l=Ubuntu,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/multiverse i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=multiverse,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=multiverse,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/universe i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=universe,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=universe,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/restricted i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=restricted,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/restricted amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=restricted,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/main i386 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=main,b=i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>&nbsp;500 http://br.archive.ubuntu.com/ubuntu focal/main amd64 Packages  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;release v=20.04,o=Ubuntu,a=focal,n=focal,l=Ubuntu,c=main,b=amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;origin br.archive.ubuntu.com  
>Pacotes alfinetados ("pinned"):  

A listagem mostra os repositórios por arquitetura (acima amd64 e i386).

## 2.INXI

É um comando para obter informações sobre a máquina. 

É uma ferramenta já construída com o propósito de fornecer relatórios para suporte. Porém, não vem instalado por padrão 
na maioria dos sistemas, embora geralmente faça parte dos repositórios. Nesse caso, é necessário instalar via:

> leandro@leandro:~$ **sudo apt install inxi**

Para obter a lista dos repositórios, é necessário executar o comando com a opção **-r**

>leandro@leandro:~$ **inxi -r**  
>Repos:     Active apt repos in: /etc/apt/sources.list   
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1: deb http://br.archive.ubuntu.com/ubuntu/ focal main restricted  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2: deb http://br.archive.ubuntu.com/ubuntu/ focal-updates main restricted  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3: deb http://br.archive.ubuntu.com/ubuntu/ focal universe  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4: deb http://br.archive.ubuntu.com/ubuntu/ focal-updates universe  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5: deb http://br.archive.ubuntu.com/ubuntu/ focal multiverse  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6: deb http://br.archive.ubuntu.com/ubuntu/ focal-updates multiverse  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7: deb http://br.archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8: deb http://security.ubuntu.com/ubuntu focal-security main restricted  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;9: deb http://security.ubuntu.com/ubuntu focal-security universe  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;10: deb http://security.ubuntu.com/ubuntu focal-security multiverse  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Active apt repos in: /etc/apt/sources.list.d/google-chrome.list   
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1: deb [arch=amd64] https://dl.google.com/linux/chrome/deb/ stable main  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Active apt repos in: /etc/apt/sources.list.d/ondrej-ubuntu-php-focal.list   
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1: deb http://ppa.launchpad.net/ondrej/php/ubuntu focal main  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;No active apt repos in: /etc/apt/sources.list.d/thopiekar-ubuntu-miraclecast-focal.list   
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Active apt repos in: /etc/apt/sources.list.d/ubuntuhandbook1-ubuntu-avidemux-focal.list   
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1: deb http://ppa.launchpad.net/ubuntuhandbook1/avidemux/ubuntu focal main  

Quando o usuário não consegue nem mesmo executar **apt update**, será quase impossível rodar o **inxi**.

O relatório mostra apenas os repositórios(não separa por arquitetura).

## CONCLUSÃO

Quando o usuário mistura repositórios de diferentes versões ou mesmo de diferentes distribuições Linux pode ser necessário
gerar uma listagem para conferência.

Há dois aplicativos de linha de comando que podem ser usados para gerar essa listagem: o comando **apt** e o comando **inxi**.

Quando o usuário não consegue nem mesmo atualizar a lista de pacotes disponíveis, usar o comando **apt** é mais vantajoso, 
visto que já vem instalado por padrão nas distribuições derivadas do **DEBIAN**.
