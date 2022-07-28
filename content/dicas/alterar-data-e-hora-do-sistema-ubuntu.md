---
title: Alterando a data e hora de uma máquina Ubuntu Linux 
date: 2022-06-01
tags:
  - "ubuntu"
  - "linux"
  - "terminal"	
categories:
  - "Linux"
---
Recentemente eu estava fazendo testes no meu sistema de bolão para a Copa do Mundo e precisei alterar a data do sistema para 2018 de modo a averiguar as validações de data. 
Foi então que percebi que não conseguia fazer isso com facilidade com date/hwclock como fazia antigamente. 
<!--more-->
Bem, eu havia criado uma regra de validação para que não fosse permitido registrar um palpite para uma partida já encerrada. Um dos critérios para definir isso era a data da partida, ou seja, não é possível alterar a 
data de uma partida no passado, que já passou.

Então eu queria ajustar a data do servidor para 13/06/2018 (um dia antes da abertura da Copa do Mundo de Futebol). Primeiro, verificando a data atual:
>leandro@leandro:~$ date   
>ter 31 mai 2022 14:40:57 -03  

Bem, mudando a data (método antigo) :
>leandro@leandro:~$ sudo date -s '20180613'  
>qua 13 jun 2018 00:00:00 -03  

Mas verificando novamente a data...
>leandro@leandro:~$ date  
>ter 31 mai 2022 14:42:50 -03  

A data não foi alterada!!!

O motivo disso ter acontecido é que a data e a hora do Ubuntu Linux 20.04 é gerenciada por um serviço de sistema chamado systemd-timesyncd responsável por
automatizar o ajuste de horário na máquina a partir da Internet sem a necessidade de intervenção do usuário.  A sincronização é realizada com um servidor NTP (Network Time Protocol) remoto. 

>leandro@leandro:~$ systemctl status systemd-timesyncd  
>● systemd-timesyncd.service - Network Time Synchronization  
>     Loaded: loaded (/lib/systemd/system/systemd-timesyncd.service; enabled; vendor preset: enabled)  
>     Active: **active (running)** since Tue 2022-05-31 13:30:53 -03; 1h 17min ago  
>       Docs: man:systemd-timesyncd.service(8)  
>   Main PID: 562 (systemd-timesyn)  
>     Status: "Initial synchronization to time server [2620:2d:4000:1::3f]:123 (ntp.ubuntu.com)."  
>      Tasks: 2 (limit: 4499)  
>     Memory: 1.5M  
>     CGroup: /system.slice/systemd-timesyncd.service  
>             └─562 /lib/systemd/systemd-timesyncd  
>  
>mai 31 13:30:53 leandro systemd[1]: Starting Network Time Synchronization...  
>mai 31 13:30:53 leandro systemd[1]: Started Network Time Synchronization.  
>mai 31 13:31:24 leandro systemd-timesyncd[562]: Initial synchronization to time server [2620:2d:4000:1::3f]:123 (ntp.ubuntu.com).  

Como podemos ver pela listagem, ele está ativo e operacional e podemos ver com qual servidor o sistema fez a sincronização (ntp.ubuntu.com - 2620:2d:4000:1::3f)
Aqui já temos a resposta para a charada. Basta parar o serviço e usar o método anterior. Felizmente, o SystemD fornece comandos para lidar com os serviços mais comuns de forma menos agressiva, 
de modo a facilitar o uso do sistema. Neste caso, o linux
possui o comando **timedatectl** para fazer a interface entre o usuário e o SystemD. Para ver se o comando está instalado:
>leandro@leandro:~$ whereis timedatectl  
>timedatectl: **/usr/bin/timedatectl** /usr/share/man/man1/timedatectl.1.gz  

O comando timedatectl pode ser usado para pesquisar e modificar o relógio do sistema e suas configurações, além de habilitar e desabilitar os serviços de sincronização. Rodando sem qualquer argumento, temos:
>leandro@leandro:~$ timedatectl  
>               Local time: ter 2022-05-31 14:55:50 -03   
>           Universal time: ter 2022-05-31 17:55:50 UTC   
>                 RTC time: ter 2022-05-31 17:55:50  
>                Time zone: America/Sao_Paulo (-03, -0300)  
>**System clock synchronized: yes**  
>              NTP service: active   
>          RTC in local TZ: no  

Podemos ver em destaque que a sincronização está habilitada. Agora vamos ver o status geral do serviço e obter algumas informações sobre o funcionamento:
>leandro@leandro:~$ timedatectl timesync-status  
>       **Server: 2620:2d:4000:1::3f (ntp.ubuntu.com)**  
>Poll interval: 32s (min: 32s; max 34min 8s)  
>         Leap: normal  
>      Version: 4  
>      Stratum: 2  
>    Reference: 11FD227B  
>    Precision: 1us (-25)  
>Root distance: 1.441ms (max: 5s)  
>       Offset: -208.577ms  
>        Delay: 787.480ms  
>       Jitter: 438.182ms  
> Packet count: 92  
>    Frequency: -500,000ppm  

Temos a mesma informação de qual o servidor está sendo usado para sincronização (ntp.ubuntu.com - 2620:2d:4000:1::3f) e muito mais detalhes. É um método bem mais elegante de obter essa informação!
Mas nosso interesse aqui não é conhecer o funcionamento do serviço, mas apenas configurar a data e hora do sistema para um valor inválido: 13 de junho de 2018. Então primeiro desabilitamos a sincronização automática:
>leandro@leandro:~$ sudo timedatectl set-ntp false  
>[sudo] senha para leandro:  
>leandro@leandro:~$  

Como essa é uma tarefa administrativa, é necessário permissão de administrador(root) no sistema. A seguir configuramos nosso valor inválido para a data do sistema:
>sudo timedatectl set-time '2018-01-01 12:00:00'  
>[sudo] senha para leandro:   
>leandro@leandro:~$  

Conferindo o resultado com o comando **date** como no começo do artigo:
>leandro@leandro:~$ date  
>seg 01 jan 2018 12:00:01 -02  

conferindo o status do serviço de sincronização podemos ver que não teremos problemas novamente, o sistema não vai corrigir nosso "problema":
>leandro@leandro:~$ timedatectl status  
>               Local time: seg 2018-01-01 12:00:27 -02  
>           Universal time: seg 2018-01-01 14:00:27 UTC   
>                 RTC time: seg 2018-01-01 14:00:27  
>                Time zone: America/Sao_Paulo (-02, -0200)  
>System clock synchronized: no  
>              **NTP service: inactive**  
>          RTC in local TZ: no  


Quando terminarmos nosso serviço e for necessário voltar para o horário corrente, basta habilitar novamente a sincronização. 
>leandro@leandro:~$ sudo  timedatectl  set-ntp true  
>[sudo] senha para leandro:  
>leandro@leandro:~$ date  
>ter 31 mai 2022 15:12:10 -03  

Fonte:

ManPage timedatectl  
ManPage systemd-timesyncd


