---
title: Melhorando o desempenho da navegação Web do usuário movendo dados para a memória RAM 
date: 2022-11-23
tags:
  - "ubuntu"
  - "linux"
categories:
  - "Linux"
  - "Dicas"
---
Os atuais navegadores de Internet como Firefox e Chrome são verdadeiros monstros que devoram todos os recursos disponíveis em uma máquina.

Além de memória RAM, os navegadores também fazem muita Entrada/Saída de dados em disco ao gravar e recuperar dados de perfis de usuários armazenados no HD para aumentar sua funcionalidade.

Isso é fantástico, mas o disco rígido é muito mais lento que a memória RAM e, em um HD com blocos defeituosos, como pode acontecer nas estações de trabalho caseiras, isso pode ser problemático.

Agora imagine se pudéssemo mover o gerenciamento desses perfis para a memória RAM e diminuíssemos a quantidade de acesso a disco. Será que melhoraríamos a performance?
<!--more-->

Essa técnica não é novidade e está disponível pela Internet, ou seja, trata-se de uma configuração do Mozila Firefox que pode ser ajustada conforme a necessidade.

Porém, há um aplicativo disponível nos repositórios que faz isso de forma automática. Trata-se do pacote **profile-sync-daemon**. 

Podemos conferir abaixo se ele está disponível para o Ubuntu:

>leandro@leandro:~$ **apt-cache search profile-sync-daemon**  
>profile-sync-daemon - Symlink and sync browser profile directories into RAM  

Vamos conferir então os detalhes sobre o pacote:

>leandro@leandro:~$ **apt show profile-sync-daemon**  
>Package: profile-sync-daemon  
>Version: 6.34-1  
>Priority: optional  
>Section: universe/utils  
>Origin: Ubuntu  
>Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>  
>Original-Maintainer: Jan Luca Naumann <j.naumann@fu-berlin.de>  
>Bugs: https://bugs.launchpad.net/ubuntu/+filebug  
>Installed-Size: 97,3 kB  
>Depends: rsync, init-system-helpers (>= 1.52)  
>Suggests: libpam-systemd, systemd-sysv  
>Homepage: https://github.com/graysky2/profile-sync-daemon  
>Download-Size: 22,1 kB  
>APT-Sources: http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  
>Description: Symlink and sync browser profile directories into RAM  
> Profile-sync-daemon (psd) is a tiny pseudo-daemon designed  
> to manage your browsers profile in tmpfs and periodically  
> sync it back to disk.  
> .  
> This is accomplished by symlinking and the innovative use  
> of rsync to maintain a backup and synchronization between  
> tmpfs and disk. One of the major design goals of psd is a  
> completely transparent user experience.  

Podemos ver que o pacote permite mover os dados de perfis de usuário no navegador para a memória através do tmpfs. Ele permite controlar os principais navegadores disponíveis no mundo Linux
como Firefox e Chrome entre outros.

Como parte de seu funcionamento, um serviço chamado **psd** será instalado para fazer periodicamente o trabalho por baixo dos panos.

>leandro@leandro:~$ **systemctl status --user psd**  
>● psd.service - Profile-sync-daemon  
>     Loaded: loaded (/usr/lib/systemd/user/psd.service; enabled; vendor preset: enabled)  
>     Active: inactive (dead)  
>       Docs: man:psd(1)  
>             man:profile-sync-daemon(1)  
>             https://wiki.archlinux.org/index.php/Profile-sync-daemon  


Um detalhe importante que pode ser visto acima é que o serviço é instalado e executado no perfil do usuário logado. Isso significa que o usuário vai poder derrubar e levantar o serviço
sem necessidade de senha de administrado (root), como podemos ver abaixo:

>leandro@leandro:~$ **systemctl start --user psd**  
>leandro@leandro:~$ **systemctl status --user psd**  
>● psd.service - Profile-sync-daemon  
>     Loaded: loaded (/usr/lib/systemd/user/psd.service; enabled; vendor preset: enabled)  
>     Active: active (exited) since Wed 2022-10-26 09:26:24 -03; 1s ago  
>       Docs: man:psd(1)  
>             man:profile-sync-daemon(1)  
>             https://wiki.archlinux.org/index.php/Profile-sync-daemon  
>    Process: 30450 ExecStart=/bin/true (code=exited, status=0/SUCCESS)  
>   Main PID: 30450 (code=exited, status=0/SUCCESS)  
>  
>out 26 09:26:24 leandro systemd[819]: Starting Profile-sync-daemon...  
>out 26 09:26:24 leandro systemd[819]: Finished Profile-sync-daemon.  

Além da Unit, o pacote também instala um aplicativo para fazer o gerenciamento da ferramenta, o comando **psd**:

>leandro@leandro:~$ **psd preview**  
>Profile-sync-daemon v6.34 on Ubuntu 20.04.4 LTS  
>  
> Systemd service is currently active.  
> Systemd resync-timer is currently active.  
> Overlayfs technology is currently inactive.  
>  
>Psd will manage the following per /home/leandro/.config/psd/psd.conf:  
>  
>  
> browser/psname:  firefox/firefox  
> owner/group id:  leandro/1000  
> sync target:     /home/leandro/.mozilla/firefox/crtz3q44.default  
> tmpfs dir:       /run/user/1000/leandro-firefox-crtz3q44.default  
> profile size:    8,0K  
> recovery dirs:   none  
>  
> browser/psname:  firefox/firefox  
> owner/group id:  leandro/1000  
> sync target:     /home/leandro/.mozilla/firefox/zsem0zxq.default-release  
> tmpfs dir:       /run/user/1000/leandro-firefox-zsem0zxq.default-release  
> profile size:    254M  
> recovery dirs:   none  
>    
> browser/psname:  google-chrome/chrome  
> owner/group id:  leandro/1000  
> sync target:     /home/leandro/.config/google-chrome  
> tmpfs dir:       /run/user/1000/leandro-google-chrome  
> profile size:    219M  
> recovery dirs:   none  


Podemos ver acima que perfis no Firefox e no Chrome estão sendo gerenciados. Além disso, durante a instalação foi criado um diretório **psd** dentro da subdiretório **.config**
na pasta raiz do usuário (leandro, neste caso) - /home/leandro/.config/psd. E dentro dessa pasta reside o arquivo **psd.conf** que contém as configurações efetivamente modificadas 
pelo comando **psd**.

# Conclusão

Vimos como otimizar uma configuração por meio de um pacote disponível nos repositórios oficiais do Ubuntu. 

Apesar disso, essa configuração pode ser modificada manualmente.

Evitar o acesso ao disco rígido pode deixar o sistema ligeiramente mais rápido ou, se for o caso, mais confiável se o HD não estiver em sua saúde perfeita.

É importante mencionar que o aplicativo pode ajustar um ou para vários navegadores, permitindo controlar onde a configuração será aplicada.


