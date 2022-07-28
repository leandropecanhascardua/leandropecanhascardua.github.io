---
title: Rastreando sniffer encondido na rede usando Linux
date: 2022-06-19
tags:
  - "Debian"
  - "Mint"
  - "linux"
  - "terminal"	
  - "rede"	
  - "segurança" 
  - "nmap"
  - "sniffer"
  - "hacker"	
categories:
  - "Linux"
---
A técnica de sniffer é considerada uma das técnicas mais sutis e difíceis de identificar porque o atacante fica imóvel na rede, atuando passivamente enquanto vigia o tráfego de dados. 

Ela consiste em colocar a placa de rede em modo promíscuo e assim capturar todos os pacotes que passam na interface de comunicação, acessando, dessa forma, dados valiosos.

Como veremos nesta dica, identificar um sniffer é difícil, mas não impossível.
<!--more-->
Para isso vamos usar o nmap e um recurso pouco conhecido dele, o **Nmap Scripting Engine(NSE)**. Ele permite que usuários escrevam scripts para automatizar tarefas de rede. 

Muitos desses scripts são disponibilizados para a comunidade e vêm, inclusive, junto com a ferramenta. No meu sistema, os scripts estão disponíveis em **/usr/share/nmap/scripts**,

Então, primeiro vamos realizar algumas atividade preliminares e  analisar nosso ambiente:

Talvez o nmap não esteja instalado. Para instalar basta fazer:
> leandro@casa:~$ sudo apt install nmap

Vamos rodar preliminarmente o nmap para verificarmos quantas máquinas temos em nossa rede:

>leandro@casa:~$ **sudo nmap -sP  192.168.15.2-10**  
>Starting Nmap 7.80 ( https://nmap.org ) at 2022-06-19 21:27 -03  
>Nmap scan report for 192.168.15.2  
>Host is up (0.00021s latency).  
>MAC Address: 08:00:27:0A:77:54 (Oracle VirtualBox virtual NIC)  
>Nmap scan report for 192.168.15.9  
>Host is up.  
>Nmap done: 9 IP addresses (2 hosts up) scanned in 26.31 seconds  
  
Agora vamos rodar o sniffer-detect para ver o que encontramos:

>leandro@casa:~$ **sudo nmap --script=sniffer-detect  192.168.15.2-10**  
>Starting Nmap 7.80 ( https://nmap.org ) at 2022-06-19 21:28 -03  
>Nmap scan report for 192.168.15.2  
>Host is up (0.00085s latency).  
>All 1000 scanned ports on 192.168.15.2 are closed  
>MAC Address: 08:00:27:0A:77:54 (Oracle VirtualBox virtual NIC)  
>  
>Nmap scan report for 192.168.15.9  
>Host is up (0.000016s latency).  
>Not shown: 999 closed ports  
>PORT   STATE SERVICE  
>80/tcp open  http  

Nmap done: 9 IP addresses (2 hosts up) scanned in 30.14 seconds

Podemos ver que há duas máquinas ativas e nada de anormal. O IP 192.168.15.9 é o anfitrião e roda Linux Mint enquanto o IP 192.168.15.2 é uma máquina virtual rodando Debian 11 no VirtualBox. 

Então este será o nosso atacante. Precisamos rodar um sniffer nele, vamos simplificar e rodar o **Tcpdump**. No Debian o comando para instalação como usuário root é o seguinte:

>root@debian01:~# apt install tcpdump 

No Ubuntu e no Linux Mint pode ser

>leandro@leandro:~# sudo apt install tcpdump 

Então rodamos nosso sniffer apenas digitando tcpdump na linha de comando e dando enter como usuário **root**

![Sniffer](/img/post/dica02/sniffer.png)

Agora agora nossa rede está insegura! Vamos ver se conseguimos encontrar o problema!

O script NSE que nos interessa é o **sniffer-detect**. É bom lembrar que o script foi criado para uma rede cabeada Ethernet. 

Isso significa que escanear uma rede wifi vai trazer resultados estranhos (na minha casa apontou que a Smartv estava snifando a rede...). Enfim, dada a ressalva, podemos executá-lo assim:

>leandro@casa:~$ **sudo nmap --script=sniffer-detect  192.168.15.2-10**  
>Starting Nmap 7.80 ( https://nmap.org ) at 2022-06-19 21:30 -03  
>Nmap scan report for 192.168.15.2  
>Host is up (0.0011s latency).  
>All 1000 scanned ports on 192.168.15.2 are closed  
>MAC Address: 08:00:27:0A:77:54 (Oracle VirtualBox virtual NIC)  
>  
>**Host script results:**  
>**|_sniffer-detect: Likely in promiscuous mode (tests: "11111111")**  
>  
>Nmap scan report for 192.168.15.9  
>Host is up (0.000016s latency).  
>Not shown: 999 closed ports  
>PORT   STATE SERVICE  
>80/tcp open  http  
>  
>Nmap done: 9 IP addresses (2 hosts up) scanned in 28.09 seconds  

Como podemos ver, o **nmap** encontrou atividade suspeita após mostrar o primeiro host (192.168.15.2).

Então a princípio temos um sniffer e nosso suspeito é o host 192.168.15.2. Então vamos rodar o script individualmente contra ele para ver:

>leandro@casa:~$ **sudo nmap --script=sniffer-detect  192.168.15.2**  
>[sudo] senha para leandro:  
>Starting Nmap 7.80 ( https://nmap.org ) at 2022-06-19 21:57 -03  
>Nmap scan report for 192.168.15.2  
>Host is up (0.00097s latency).  
>All 1000 scanned ports on 192.168.15.2 are closed  
>MAC Address: 08:00:27:0A:77:54 (Oracle VirtualBox virtual NIC)  
>  
>**Host script results:**  
>**|_sniffer-detect: Likely in promiscuous mode (tests: "11111111")**  
>  
>Nmap done: 1 IP address (1 host up) scanned in 14.79 seconds  

O resultado parece confirmar nossa suspeita. Podemos rodar contra o outro host(192.168.15.9) para conferir:

>leandro@casa:~$ **sudo nmap --script=sniffer-detect  192.168.15.9**  
>Starting Nmap 7.80 ( https://nmap.org ) at 2022-06-19 21:32 -03  
>Nmap scan report for 192.168.15.9  
>Host is up (0.000015s latency).  
>Not shown: 999 closed ports  
>PORT   STATE SERVICE  
>80/tcp open  http  
>  
>Nmap done: 1 IP address (1 host up) scanned in 13.77 seconds  


Se quiser analisar o código fonte do script - ou de qualquer outro - basta acessar a pasta **/usr/share/nmap/scripts**

>leandro@casa:/usr/share/nmap/scripts$ ls sniffer-detect.nse -l  
>-rw-r--r-- 1 root root 4327 mar 23  2020 **sniffer-detect.nse**  

# Conclusão
O nmap, que é um software amplamente conhecido como ferramente preliminar a um ataque, pode ser usado também como ferramente de defesa pelos administradores de rede.

Esse script, contudo, foi criado para verificar uma rede Ethernet (cabeada). Rodar o script contra uma rede WIFI pode trazer resultados estranhos e inesperados, 
então o uso mais apropriado seria numa rede Ethernet corporativa.

Além disso, o script utiliza heurísticas para descobrir o sniffer, então pode haver casos de falsos negativos e falsos positivos, mas é um excelente ponto de partida para investigação.

O código fonte do script está disponível no diretório informado no corpo do artigo para quem quiser conhecer a implementação.

