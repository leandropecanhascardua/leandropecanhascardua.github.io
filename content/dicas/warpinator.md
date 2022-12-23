---
title: Compartilhamento (fácil) de arquivos na rede local (na moleza mesmo!)
date: 2022-12-23
tags:
  - "ubuntu"
  - "linux mint"
  - "linux"
categories:
  - "Linux"
  - "Dicas"
---
Hoje em dia é comum a existência e o uso de vários dispositivos de informática simultaneamente e com eles a necessidade de trocar arquivos.

Seja em casa, seja em um escritório. Podemos estar em qualquer lugar e acessar a informação de qualquer dispositivo!

Por isso uma necessidade que surge é de um modo de transferir arquivos de um local para outro, de preferência a um custo baixo e
sem a necessidade de conhecimentos técnicos para sua implantação.
<!--more-->
Afinal, podemos baixar um boleto no celular mas visualizar em um computador; ou baixar um arquivo em um computador Linux e acessá-lo em outro rodando Windows.
São diversas as situações onde surge essa necessidade.

Há várias possibilidades de resolver esse problema: Podemos configurar pastas compartilhadas, podemos implantar um servidor de arquivos intranet, podemos usar um serviço de 
arquivos em nuvem(como Onedrive, Google Drive etc).

São todas possibilidades válidas. Cada uma tem prós e contras. Mas eu quero indicar uma solução usando comunicação P2P e, o melhor de tudo! Livremente disponível e já instalado por padrão 
em alguns sistemas, como, por exemplo, o Linux Mint. Trata-se do **Warpinator**!

O **Warpinator** permite compartilhar arquivos entre dispositivos dentro de uma mesma rede usando transmissão P2P (ponto a ponto). Isso significa que podemos integrar computadores de mesa,
laptops, aparelhos celulares e até mesmo televisores, se tiverem Android como sistema operacional.

E além disso, podemos integrar qualquer distribuição Linux, sistemas Android e até máquinas Windows! É por isso que eu considero a solução ideal para redes domésticas.

É claro que em um ambiente empresarial pode não ser a solução ideal tendo em vista que, neste caso, o Warpinator chega a ser ingênuo, mas um
ambiente doméstico é mais do que ideal para ele!

## Instalação

No Linux Mint, essa maravilha vem instalada por padrão, não sendo necessário fazer nada além de configurar.

Por isso vou demonstrar a instalação em um sistema Ubuntu. A instalação para outras distribuições Linux pode ser encontrada [aqui](https://github.com/linuxmint/warpinator).

Primeiro, instalando algumas dependências:

> leandro@leandro:~/Downloads$ **sudo apt-get install python3-grpc-tools python3-grpcio**  
> leandro@leandro:~/Downloads$ **sudo apt-get -y install debhelper dh-python gnome-pkg-tools meson gobject-introspection appstream python3-grpc-tools**

Baixando o código fonte do repositório via git:

> leandro@leandro:~/Downloads$ **git clone https://github.com/linuxmint/warpinator.git**

Construindo um pacote .deb:

>leandro@leandro:~/Downloads$ **cd WARPINATOR**  
>leandro@leandro:~/Downloads/WARPINATOR$ **dpkg-buildpackage --no-sign**

Aqui uma pequena mudança em relação à instalação sugerida no github. Vou usar o gerenciador de pacotes APT para resolver as dependências durante a instalação:

> leandro@leandro:~/Downloads/WARPINATOR$ **sudo apt install -f ./warpinator_1.4.3_all.deb**

Depois disso um item foi automaticamente instalado no menu dentro do item "**Acessórios**" com o nome "**Warpinator**".

Os dispositivos Android podem fazer a instalação a partir do [Google Play](https://play.google.com/store/apps/details?id=slowscript.warpinator&hl=pt-BR).

## Utilização

Ao abrir ele deve aparecer mais ou menos assim:

![teste](/img/post/dica06/w01.png)

Nos informando que também há versões para **Android**, **IOS** e **Windows**. Além disso, também podemos ver que nossa rede não possui outras máquinas (ou pelo menos não foram reconhecidas).

Vamos clicar no ícone de engrenagem no canto superior esquerdo e ajustar as configurações:

![teste](/img/post/dica06/w02.png)

Podemos ver duas abas: **Em geral** e **Conexão**.

Dentro da aba **Em geral**, as configurações que nos interessam é **Iniciar automaticamente**, **Use compressão quando possível** e **Diretório para arquivos recebidos**. Acho que o nome
deixa óbvio a intenção de cada. Item. 

Selecionando a aba **Conexão**:

![teste](/img/post/dica06/w03.png)

O ponto mais importante aqui é o **Código de Grupo**. Esse código define um grupo. 

Isso significa que dentro de uma rede podemos criar vários grupos e nos conectarmos com um grupo específico.

Como eu já disse, isso não é mecanismo de segurança, é apenas para permitir ao usuário segmentar a rede segundo um critério. 

O nome do grupo deve ser o mesmo em todas as máquinas que poderão enviar ou receber arquivos.

Outra coisa importante a se notar é que o Warpinator usa sockets de rede para se comunicar (por padrão as portas 42000 e 42001). Isso significa que todas as máquinas devem ter essas portas
livres para acesso nos firewalls locais e no de rede.

As máquinas que fizerem parte do grupo aparecerão na tela inicial do Warpinator em formato de lista, como a seguir:

![teste](/img/post/dica06/w04.png)

Cada linha conterá o nome da máquina, o usuário logado e ip/porta. Para enviar um arquivo para a máquina, basta dar um clique na linha correspondente. Seremos encaminhados para outra
tela:

![teste](/img/post/dica06/w05.png)

Onde podemos ver as transferências já realizadas  e botão para selecionar arquivos. Se enviarmos um arquivo, veremos algo como:

![teste](/img/post/dica06/w06.png)

Sinal de que tudo deu certo. Em caso de falha, é sempre bom checar de o firewall de todas as máquinas permitem acesso livre às portas do Warpinator e também é bom verificar o código 
de grupo. Essas são as maiores fontes de problemas.

## conclusão

Compartilhar arquivos é uma necessidade preemente atualmente. Seja laptop, celular ou computador de mesa, a informação está distribuída pela rede.

Existem muitas formas de realizar esse compartilhamento e vimos nesta dica o Warpinator, uma aplicativo que vem instalado por padrão no Linux Mint e que permite compartilhar arquivos
entre vários dispositivos e vários sistemas operacionais, como android, IOS ou Windows. É uma excelente ferramenta para ambientes domésticos pela sua simplicidade e fácil configuração.




