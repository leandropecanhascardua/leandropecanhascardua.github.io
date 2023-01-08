---
title: Trasmitindo vídeos do computador e assistindo no celular dentro da Intranet usando VLC (e Linux!)
date: 2023-01-07
tags:
  - "ubuntu"
  - "linux mint"
  - "linux"
  - "vlc"
categories:
  - "Linux"
  - "Dicas"
---
Hoje em dia estamos cercados de aparelhos eletrônicos. De celulares a smartvs, passando por laptops e computadores de mesa.

Nesse cenário, compartilhar informação e dados é uma necessidade diária. Inclusive filmes e séries!
Às vezes precisamos assistir no celular ou tablet um filme que baixamos em um computador. 

Pouca gente sabe, mas o VLC fornece uma solução simples e rápida para isso!
<!--more-->
O VLC media player é um reprodutor multimídia, isto é, ele pode reproduzir filmes ou músicas; é livre e desenvolvido em várias plataformas, como Windows, Linux, Mac Os X entre outros.

Uma questão interessante é que ele pode servir também como um servidor de streaming simples, e é isso que vamos implementar nesta dica.

Meu propósito é o de transmitir um único filme pela minha rede doméstica. Ou seja, o usuário final não vai ter a opção de selecionar o que vai assistir. Além disso, como mencionado,
o usuário deve estar na mesma rede intranet do servidor.

Esse é um cenário bem limitado, para cenários mais avançados é mais interessante ter um servidor de mídia como Jellyfin, Kodi ou Rygel. Para mim, o cenário básico é o suficiente por enquanto.

## Instalação.

No Ubuntu 20.04, usado como demonstração neste artigo, o VLC pode ser instalado diretamente dos repositórios:

> sudo apt update  
> sudo apt install vlc  

Depois de instalado ele estará disponível no item Multimídia do menu principal. 

## Iniciando o Servidor

Ao abrir o VLC na máquina que hospeda nosso arquivo de mídia, veremos algo como:

![vlc](/img/post/dica07/vlc01_server.png)

Para iniciar nossa transmissão, selecionamos **"Abrir Fluxo de Rede"** dentro do item **"Mídia"** no menu principal e escolhemos a aba **"Arquivo"**, como visto na imagem seguinte:

![vlc](/img/post/dica07/vlc03_server.png)

Nesta tela clicamos no botão **"Add..."** para selecionar o arquivo que vamos transmitir

![vlc](/img/post/dica07/vlc04_server.png)

Uma vez selecionado o arquivo, se tivermos um arquivo de legenda, podemos adicionar marcando o item "Use a subtitle file", que vai habilitar o campo de busca para o arquivo logo abaixo.

![vlc](/img/post/dica07/vlc05_server.png)

O botão **"Reproduzir"** na imagem acima na verdade é um menu, onde devemos selecionar "fluxo". Isso nos levará para a próxima tela:

![vlc](/img/post/dica07/vlc06_server.png)

Nessa tela não mudaremos nada. Clicando no botão  **"Próximo"** prosseguimos a configuração. 

Agora devemos escolher o protolocol que vai ser usado na transmissão, ou seja, a forma como o arquivo vai ser transmitido. Para o nosso exemplo selecionamos **"HTTP"** e clicamos no botão "Próximo".

![vlc](/img/post/dica07/vlc08_server.png)

Onde apenas clicamos no botão "Próximo". 

A tela seguinte nos permite configurar a porta onde nosso streaming vai executar e o caminho se o usuário quiser. Vamos deixar o padrão. 

![vlc](/img/post/dica07/vlc09_server.png)

Importante lembrar que se o usuário não deve ter outro serviço executando na máquina nesta porta (neste caso, 8080), 
do contrário o serviço não vai subir. 

Uma forma de saber se já existe um serviço usando a porta desejada é usar o comando nmap, como a seguir:

>leandro@leandro:~$ **nmap localhost -p 8080**  
>Starting Nmap 7.80 ( https://nmap.org ) at 2023-01-07 18:45 -03  
>Nmap scan report for localhost (127.0.0.1)  
>Host is up (0.00018s latency).  
>  
>PORT     STATE  SERVICE  
>8080/tcp closed http-proxy  
>  
>Nmap done: 1 IP address (1 host up) scanned in 0.12 seconds  
  
Podemos ver que a porta 8080 está aberta e há um servidor http escutando. Clicando no botão "Próximo", chegamos em:

![vlc](/img/post/dica07/vlc10_server.png)

Nesta tela devemos desativar a opção **"Activate Transcoding"**e em perfil podemos selecionar **"Vídeo-H.264 + MP3(MP4)"**, essa é o codec usado pelo Youtube para transmissão.

Clicando em **"Próximo"** chegamos na última tela, onde podemos configurar algumas coisas adicionais. No meu caso não precisei modificar nada e apenas cliquei em **"Fluxo"**.

Neste momento o usuário pode achar que tudo deu errado porque não acontece nada! Aparentemente... 

Mas rodando o nmap novamente, podemos ver nosso servidor aberto. 

Como passo adicional precisamos do endereço do servidor para que ele possa ser encontrado pela rede. Para isso podemos o comando **ifconfig** ou o comando **ip**

>leandro@leandro:~$ **ifconfig**  
>(...)
>wlp1s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500  
>        inet **192.168.15.9**  netmask 255.255.255.0  broadcast 192.168.15.255  
>        inet6 fe80::8b07:cbf6:d702:dc0d  prefixlen 64  scopeid 0x20<link>  
>        inet6 2804:7f2:69f:2875:442c:38d8:a014:1c08  prefixlen 64  scopeid 0x0<global>  
>        inet6 2804:7f2:69f:2875:8344:6286:c945:2e62  prefixlen 64  scopeid 0x0<global>  
>        ether 54:27:1e:3c:ad:ee  txqueuelen 1000  (Ethernet)  
>        RX packets 41491  bytes 27972615 (27.9 MB)  
>        RX errors 0  dropped 0  overruns 0  frame 0  
>        TX packets 36799  bytes 8206497 (8.2 MB)  
>        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0  

>leandro@leandro:~$ **ip a**  
>(...)  
>3: wlp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000  
>    link/ether 54:27:1e:3c:ad:ee brd ff:ff:ff:ff:ff:ff  
>    inet **192.168.15.9**/24 brd 192.168.15.255 scope global dynamic noprefixroute wlp1s0  
>       valid_lft 39664sec preferred_lft 39664sec  
>    inet6 2804:7f2:69f:2875:8344:6286:c945:2e62/64 scope global temporary dynamic   
>       valid_lft 43198sec preferred_lft 43198sec  
>    inet6 2804:7f2:69f:2875:442c:38d8:a014:1c08/64 scope global dynamic mngtmpaddr noprefixroute   
>       valid_lft 43198sec preferred_lft 43198sec  
>    inet6 fe80::8b07:cbf6:d702:dc0d/64 scope link noprefixroute   
>       valid_lft forever preferred_lft forever  

Uma coisa importante a destacar é que se o computador que fizer a transmissão estiver configurado para obter IP dinâmico (DHCP) o endereço pode mudar após cada reboot e,
consequentemente, será necessário reconfigurar novamente  o cliente. 

Por isso pode ficar interessante configurar um IP estático na máquina servidora para evitar este problema.

No meu caso, como demonstrado na saída dos comandos acima, o endereço do meu servidor é **192.168.15.9**.
Agora precisamos de um cliente!!!!

# Instalando o Cliente

O cliente será um smartphone com Android, mas poderia ser uma smartv. Para isso basta ter o endereço do servidor.

Vamos baixar a versão do VLC para Android na PlayStore no Google (https://play.google.com/store/apps/details?id=org.videolan.vlc&hl=pt_BR&gl=US)

Uma vez instalado ele vai se parecer como abaixo:

![vlc](/img/post/dica07/Vlc-client01.jpg )

Podemos usar as funções comuns e selecionar vídeos ou músicas, mas nosso foco agora é assistir streaming de vídeo. Vamos clicar no imenso botão "Novo Transmissão" na metade da tela.

Na tela seguinte basta configurar endereço e porta do streaming. Como visto na seção anterior, minha máquina de tranmissão é 192.168.15.9 e a porta é a padrão 8080. Logo devemos preencher 
como o valor **http://192.168.15.9:8080** conforme a imagem abaixo:

![vlc](/img/post/dica07/Vlc-client02.jpg)

Se não houver firewall bloqueando portas na máquina servidora o vídeo deve começar imediatamente:

![vlc](/img/post/dica07/Vlc-client03.jpg)

E além disso deve aparecer um atalho na área de trabalho do aplicativo, conforme abaixo:

![vlc](/img/post/dica07/Vlc-client04.jpg)

Aqui se mostra a vantagem de configurar um endereço estático para a máquina servidora. Dessa forma, da próxima vez que formos assistir streaming vindo desta máquina basta clicar no atalho!


## Conclusão

A quantidade de equipamentos eletrônicos ao nosso dispor aumenta dia após dia.

Um cenário comum hoje é baixar filmes em um computador desktop, que possui maior armazenamento e pode se beneficar de uma conexão Ethernet mais rápida via cabo 
e assistir deitado confortavelmente com um aparelho celular ou tablet.

O VLC é um player de vídeo muito versãtil que permite criar uma solução fácil e multiplataforma. 

