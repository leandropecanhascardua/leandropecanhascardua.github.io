---
title: Diversão para as crianças pequititinhas no Linux
date: 2022-08-28
tags:
  - "ubuntu"
  - "linux"

categories:
  - "Linux"
  - "Dicas"
---
O Linux é um poderoso sistema operacional. É completo em todos os sentidos e atende aos mais rígidos padrões de segurança e estabilidade.

Porém, de vez em quando, é necessário relaxar um pouco. Para quem tem crianças na faixa entre três e cinco anos de idade, algumas distribuições 
fornecem um aplicativo divertido chamado oneko que pode deixar os pequenos absolutamente fascinados.
<!--more-->
O aplicativo que quero mostrar chama-se **Oneko**. Tudo o que ele faz é mostrar bichinho na tela que persegue o mouse à medida em que este se movimenta pela tela.

>leandro@leandro:~$ oneko

![Oneko](/img/post/dica04/oneko01.png)

Eis o gatinho correndo!
Para instalar no Ubuntu e no Linux Mint basta fazer:

> leandro@leandro:~$ **sudo apt install  oneko**

Para distribuições baseadas em Debian de um modo geral podemos verificar se ele faz parte do repositório de pacotes com:

>leandro@leandro:~$ **apt-cache search oneko**  
>**oneko - Gato persegue o cursor (que vira um rato) pela tela**  
>fonts-mona - Japanese TrueType font for 2ch ASCII art  
>xfonts-mona - Proportional X fonts for 2ch ASCII art  



O programa bloqueia o terminal até o termino da sua execução. Para fechar o programa, basta apertar Control+C ( com o terminal selecionado).

Podemos também iniciar o oneko pela linha de comando direto em background :

>leandro@leandro:~$ **oneko &**  
>[1] 2908  

Como padrão, o bichinho mostrado é um gato. É possível alterar o bichinho através dos parâmetros passados via linha de comando:

Para mostrar um cachorrinho:

>leandro@leandro:~$ **oneko -dog**

Para mostrar um gato mais estilizado (meio tigre):
>leandro@leandro:~$ **oneko -tora**

Para mostrar uma menina(Sakura Kinomoto, personagem de mangá):
>leandro@leandro:~$ **oneko -sakura**

Para mostrar outra menina(Tomoyo Daidouji, personagem de mangá):
>leandro@leandro:~$ **oneko -tomoyo**

É possível mudar a cor do bichinho com o parâmetro -gf
>leandro@leandro:~$ **oneko -bg blue**

É possível mudar a velocidade do bichinho com o parâmetro -speed

Um bichinho mais lento:

>leandro@leandro:~$ **oneko -speed 5**

Um bichinho mais rápido:
>leandro@leandro:~$ **oneko -speed 50**

É possível ainda alterar a velocidade do gatinho com o parâmetro -time, que configura o intervalo de tempo para animação. O valor 
padrão é 12500 e é necessário ter cuidado com valores muito baixos porque podem tornar difícil matar o processo depois, ao mesmo
tempo em que valores muito elevados podem fazer com que não seja mostrado animação alguma.

Uma configuração para teste poderia ser:

>leandro@leandro:~$ **oneko -time 50000**


Minha filha tem 03 anos e 07 meses e se divertiu bastante com o bichinho. Mas...

... mas ela se divertiu ainda mais quando eu coloquei vários bichinhos na tela!!!

Com um pouco de programação é possível criar um aplicativozinho para dar vida a uma dezena de gatinhos perseguindo o mouse!!!

Vamos criar um exército de gatinhos!!!!

## Criando um exército

Nesse caso vou lançar mão de um script shell para criar dez instâncias do aplicativo em background com um intervalor de 01 segundo
entre cada chamada para que um gato saia do lugar antes do seguinte ser criado. Vamos criar o script abrir_gatinhos.sh

``` bash
#!/bin/bash

NUM_GATINHOS=10

for i in $(seq 1 $NUM_GATINHOS); do
         /usr/games/oneko & 
        sleep 1
done
```
Após salvar na pasta ~/.local/bin/ (se quiser salvar em outra pasta será necessário adaptar todos os exemplos a partir daqui)é necessário dar permissão de execução:

>leandro@leandro:~$ **chmod +x ~/.local/bin/abrir_gatinhos.sh**

e executar no terminal:

>leandro@leandro:~$ **~/.local/bin/abrir_gatinhos.sh**

Após a criação do último bichinho todos os dez programas continuarão executando em background. Para encerrar todos basta digitar:

> leandro@leandro:~$ **killall oneko**

Vamos criar um script para automatizar a finalização também. Vamos criar o script fechar_gatinhos.sh dentro de ~/.local/bin

``` bash
#!/bin/bash

killall oneko
```

Damos permissão de execução:

>leandro@leandro:~$ **chmod +x ~/.local/bin/fechar_gatinhos.sh**

Isso é legal, mas é maçante iniciar e finalizar pelo terminal. É mais interessante criar um lançador no desktop para
facilitar as coisas. Assim não quebramos a magia para as crianças!

Vamos mover para nossa pasta ~/.local/bin e vamos salvar dois ícones para nossas duas aplicações.

leandro@leandro:~$ **mv gatinhos.sh ~/.local/bin**

Um ícone para abrir o aplicativo:
>leandro@leandro:~$  **wget https://icons.iconarchive.com/icons/iconka/meow-2/256/cat-acrobat-icon.png**

E outro para fechar:
> leandro@leandro:~$ **wget https://icons.iconarchive.com/icons/iconka/meow-2/256/cat-pirate-icon.png**


Agora vamos criar dois atalhos na àrea de trabalho: um para iniciar nosso exército de gatinhos e outro para fechar. 

Vamos criar um arquivo chamado **'Abre Gatinhos'.Desktop** na área de trabalho com o conteúdo abaixo:

``` bash
[Desktop Entry]
Exec=/home/leandro/.local/bin/abrir_gatinhos.sh
Icon=/home/leandro/.local/bin/cat-acrobat-icon.png
Name=Abre Gatinhos
Path=/home/leandro/.local/bin/

Type=Application
Comment=
Terminal=false
StartupNotify=false
```

Vamos criar um outro script com nome **'Fecha Gatinhos'.Desktop** na área de trabalho e mais um lançador para fechar nossos gatinhos:


``` bash
[Desktop Entry]
Exec=/home/leandro/.local/bin/fechar_gatinhos.sh
Icon=/home/leandro/.local/bin/cat-pirate-icon.png
Name=Fecha Gatinhos
Path=/home/leandro/.local/bin/

Type=Application
Comment=
Terminal=false
StartupNotify=false
```
Falta apenas dar permissão de execução para nossos lançadores (atalhos). Clicamos com o botão direito e selecionamos **Propriedades...**.

A seguir marcamos o campo **Permitir que este arquivo execute como um programa** como a figura abaixo:

![Oneko](/img/post/dica04/oneko02.png)

Com isso basta dar dois cliques no gatinho preto para ver nosso exército correndo pela tela e clicar duas vezes no gatinho machucado
para terminar a brincadeira.

## Conclusão

Nem só de trabalho vive o homem.

Às vezes precisamos deixar uma criança encantada. Nesta dica mostrei como usar o **Oneko**, um programa que mostra um gatinho
correndo pela tela atrás do ponteiro do mouse.

Mostrei ainda como automatizar e criar vários gatinhos correndo pela tela e como fechar tudo com apenas dois cliques.

Tudo fica ainda mais legal com dois monitores. As crianças adoram ver os gatinhos pulando de um monitor para o outro!!!!

