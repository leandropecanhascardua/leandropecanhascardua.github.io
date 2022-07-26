---
title: Encontrando erros e corrigindo problemas no Ubuntu Linux - Parte 01
date: 2022-07-25
tags:
  - "ubuntu"
  - "linux"
categories:
  - "Linux"
---
Em muitas situações o sistema operacional pode apresentar erros ou se comportar de estranhamente.

Quando isso acontece, é necessário saber onde e como encontrar pistas para diagnosticar o problema.

Nesta dica rápida vamos conhecer alguns comandos e opções para encontrar mensagens de erro fornecidas
pelo sistema em mal funcionamento.
<!--more-->

## dmesg
O comando dmesg permite controlar o buffer de mensagens do kernel. Para nós interessa saber que exibe
todas as mensagens do kernel desde o início do processo de boot.

>leandro@leandro:~$ **dmesg**  
>[    0.000000] microcode: microcode updated early to revision 0x2f, date = 2019-02-17  
>[    0.000000] Linux version 5.15.0-41-generic (buildd@lcy02-amd64-105) (gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #44~20.04.1-Ubuntu SMP Fri Jun 24 13:27:29 UTC 2022 (Ubuntu 5.15.0-41.44~20.04.1-generic 5.15.39)  
>\(...)  

Executado no terminal sem argumentos, todas as mensagens são exibidas de forma contínua. Se você quiser
examinar as mensagens paginando a saída, use o parâmetro(--human)

>leandro@leandro:~$ **dmesg --human**  
>[jul24 21:34] microcode: microcode updated early to revision 0x2f, date = 2019-02-17  
>[  +0,000000] Linux version 5.15.0-41-generic (buildd@lcy02-amd64-105) (gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #44~20.04.1-Ubuntu SMP Fr>  
>[  +0,000000] Command line: BOOT_IMAGE=/boot/vmlinuz-5.15.0-41-generic root=UUID=0d8fd723-9e58-4ec5-9345-3abe7557af80 ro quiet splash vt.handoff=7  
>[  +0,000000] KERNEL supported cpus:  
>[  +0,000000]   Intel GenuineIntel  
>(...)

Todas as mensagens originadas no kernel são exibidas, mas para nosso propósito importa apenas aquelas 
relacionadas a erros ou problemas.

Para isso, vamos usar a opção(**-l** ou **--level**) seguida por uma lista de opções relacionadas ao nível de
prioridade das mensagens.

Os níveis estão ordenados por odem de importância e são:

|  |  |
| ------- | ----------------------------------- |
|   **emerg** | sistema está inutilizável           |
|   **alert** | ação deve ser tomada imediatamente  |
|    **crit** | condições críticas                  | 
|     **err** | condições de erro                   |
|    **warn** | condições de aviso                  |
|  **notice** | condição normal, mas significativa  |
|    **info** | informativo                         |
|   **debug** | mensagens de nível de depuração     |

Para nosso propósito, as mensagens que importam são: **emerg**, **alert**, **crit**,**err**

Mensagens de um desses níveis indicam condições que devem ser resolvidas imediatamente (urgência na ordem em que são
apresentadas na tabela). Para ver problemas emergenciais(prioridade mais alta):

>leandro@leandro:~$ **dmesg --level=emerg**  
>leandro@leandro:~$  

>leandro@leandro:~$ **dmesg --level=alert**  
>leandro@leandro:~$   

>leandro@leandro:~$ **dmesg --level=crit**  
>leandro@leandro:~$   

>leandro@leandro:~$ **dmesg --level=err**  
>[    0.251635] ACPI BIOS Error (bug): Could not resolve symbol [\_SB.PCI0.LPCB.EC0._REG.CPST], AE_NOT_FOUND (20210730/psargs-330)  
>[    0.251720] ACPI Error: Aborting method \_SB.PCI0.LPCB.EC0._REG due to previous error (AE_NOT_FOUND) (20210730/psparse-529)  
>[    2.061178] ACPI BIOS Error (bug): Could not resolve symbol [\_SB.PCI0.SAT0.SPT0._GTF.DSSP], AE_NOT_FOUND (20210730/psargs-330)  
>[    2.061406] ACPI Error: Aborting method \_SB.PCI0.SAT0.SPT0._GTF due to previous error (AE_NOT_FOUND) (20210730/psparse-529)  
>[    2.110490] ACPI BIOS Error (bug): Could not resolve symbol [\_SB.PCI0.SAT0.SPT0._GTF.DSSP], AE_NOT_FOUND (20210730/psargs-330)  
>[    2.110722] ACPI Error: Aborting method \_SB.PCI0.SAT0.SPT0._GTF due to previous error (AE_NOT_FOUND) (20210730/psparse-529)  
>[  488.379893] usb 2-1.2: device descriptor read/64, error -71  
>[  660.819745] usb 2-1.2: device descriptor read/64, error -71  

Podemos ver que meu sistema não tem erros de prioridade **emerg**, **alert**, **crit**, mas tem erros
de nível **err**, que é o mais baixo com o qual devemos nos preocupar.

O ACPI é um padrão da indústria e, por isso, procura padronizar a configuração de energia no computador.

Alguns fabricantes de BIOS, para fornecer algum recurso específico, projetam seu sistema tendo como pressuposto o sistema 
operacional Windows. Vem daí a origem da mensagem acima. Isso é um problema? Pode ser ou pode não ser. Alguma coisa não está
implementada corretamente. Pode ser algo bobo como uma definição incorreta de uma variável ou pode ser uma funcionalidade
implementada de forma não padronizada. 

Incrédulo? Que tal saber a chave de instalação do Windows que veio na sua máquina? (Esse comando só vai funcionar em máquinas
vendidas com Windows pré-instalado):

>leandro@leandro:~$ sudo strings /sys/firmware/acpi/tables/MSDM | tail -n 1  
>**D6N96-4F3W9-KV7TT-MK4GP-*****   


Sua máquina foi vendida para ser usada com Windows(com algumas exceções)!!!Infelizmente as mensagens de ACPI são razoavelmente comuns em laptops como de baixo orçamento como o que eu estou
rodando . 

Na listagem acima há também erros de usb que são devidos a má conexão pois estou usando um hub usb e, portanto, a conexão é instável.

Pode ser interessante consultar todas as mensagens relevantes com um único comando. Podemos passar as 
prioridades que nos interessam como uma lista com os itens separados por vírgula:

>leandro@leandro:~$ **dmesg --level emerg,crit,alert,err**  
>[    0.251635] ACPI BIOS Error (bug): Could not resolve symbol [\_SB.PCI0.LPCB.EC0._REG.CPST], AE_NOT_FOUND (20210730/psargs-330)  
>[    0.251720] ACPI Error: Aborting method \_SB.PCI0.LPCB.EC0._REG due to previous error (AE_NOT_FOUND) (20210730/psparse-529)  
>[    2.061178] ACPI BIOS Error (bug): Could not resolve symbol [\_SB.PCI0.SAT0.SPT0._GTF.DSSP], AE_NOT_FOUND (20210730/psargs-330)  
>[    2.061406] ACPI Error: Aborting method \_SB.PCI0.SAT0.SPT0._GTF due to previous error (AE_NOT_FOUND) (20210730/psparse-529)  
>[    2.110490] ACPI BIOS Error (bug): Could not resolve symbol [\_SB.PCI0.SAT0.SPT0._GTF.DSSP], AE_NOT_FOUND (20210730/psargs-330)  
>[    2.110722] ACPI Error: Aborting method \_SB.PCI0.SAT0.SPT0._GTF due to previous error (AE_NOT_FOUND) (20210730/psparse-529)  
>[  488.379893] usb 2-1.2: device descriptor read/64, error -71  
>[  660.819745] usb 2-1.2: device descriptor read/64, error -71  

O primeiro campo da saída padrão do **dmesg** indica o timestamp desde o início do boot. Logo, quanto
menor o valor mais próximo do momento em que o usuário ligou a máquina e quanto maior mais próximo do 
momento em que o usuário executou o dmesg.

Além de erro o comando dmesg também pode ser usado para verificar ações como carregamento de drivers
em dispositivos ou montagem de sistemas de arquivos. Portanto, junto com o comando **grep** pode ajudar
bastante a vida dos usuários:

>leandro@leandro:~$ **dmesg |grep sda**  
>[    2.114043] sd 0:0:0:0: [sda] 976773168 512-byte logical blocks: (500 GB/466 GiB)  
>[    2.114053] sd 0:0:0:0: [sda] 4096-byte physical blocks  
>[    2.114086] sd 0:0:0:0: [sda] Write Protect is off  
>[    2.114097] sd 0:0:0:0: [sda] Mode Sense: 00 3a 00 00  
>[    2.114154] sd 0:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA  
>[    2.244260]  sda: sda1 sda2 sda3 sda5 sda6  
>[    2.260468] sd 0:0:0:0: [sda] Attached SCSI disk  
>[    3.335976] EXT4-fs (sda6): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.  
>[    9.944243] EXT4-fs (sda6): re-mounted. Opts: errors=remount-ro. Quota mode: none.  

No comando acima eu selecionei as mensagens do kernel relacionadas ao meu disco rígido(sda) e recebi
várias informações, como modo de escrita e as partições.


## systemctl

O SystemD é o programa **init** no Ubuntu e em várias distribuições Linux. É o programa que fica
continuamente rodando recebendo requisições do usuário, processando e enviando de volta o resultado.

Para confirmar se o sistema está usando SystemD,  podemos executar:

>leandro@leandro:~$ **ls -l /sbin/init**  
>lrwxrwxrwx 1 root root 20 abr 21 09:54 /sbin/init -> /lib/systemd/systemd  
 
Vemos que /sbin/init é um link para /lib/systemd/systemd.

Interessa-nos saber se há serviços falhando durante a inicialização. Para isso rodamos:

>leandro@leandro:~$ **systemctl --failed**  
>  UNIT                  LOAD   ACTIVE SUB    DESCRIPTION                           
>● **fwupd-refresh.service** loaded failed failed Refresh fwupd metadata and update motd  
>  
>LOAD   = Reflects whether the unit definition was properly loaded.  
>ACTIVE = The high-level unit activation state, i.e. generalization of SUB.  
>SUB    = The low-level unit activation state, values depend on unit type.  
>  
>1 loaded units listed.  


Um serviço que não carrega é um recurso sendo desperdiçado gastando tempo na inicialização da máquina,
podendo deixar o tempo de boot absurdamente maior.

Podemos ver no relatório acima que o serviço **fwupd-refresh** está falhando. podemos ver algumas 
informações sobre o serviço rodando:

>leandro@leandro:~$ **systemctl status fwupd-refresh.service**  
>● fwupd-refresh.service - **Refresh fwupd metadata and update motd**  
>     Loaded: loaded (/lib/systemd/system/fwupd-refresh.service; static; vendor preset: disabled)  
>     Active: failed (Result: exit-code) since Sun 2022-07-24 23:09:55 -03; 14min ago  
>TriggeredBy: ● fwupd-refresh.timer  
>       Docs: **man:fwupdmgr(1)**  
>    Process: 2786 ExecStart=/usr/bin/fwupdmgr refresh (code=exited, status=1/FAILURE)  
>   Main PID: 2786 (code=exited, status=1/FAILURE)  
>  
>jul 24 23:09:54 leandro systemd[1]: Starting Refresh fwupd metadata and update motd...  
>jul 24 23:09:55 leandro systemd[1]: fwupd-refresh.service: Main process exited, code=exited, status=1/FAILURE  
>jul 24 23:09:55 leandro systemd[1]: fwupd-refresh.service: Failed with result 'exit-code'.  
>jul 24 23:09:55 leandro systemd[1]: Failed to start Refresh fwupd metadata and update motd.  


Em negrito os pontos mais relevantes estão destacados. Esse serviço roda o comando **/usr/bin/fwupdmgr** que faz a atualização do
banco de dados de firmwares disponíveis.

No meu caso eu não quero esse serviço rodando, por isso vou buscar os serviços relacionados a este:

>leandro@leandro:~$ **systemctl list-units|grep fwupd**  
>● fwupd-refresh.service        loaded failed failed    Refresh fwupd metadata and update motd  
>  fwupd-refresh.timer          loaded active waiting   Refresh fwupd metadata regularly   

Vou desabilitar o serviço e o timer. O serviço é o daemon e o timer é um job que é executado de tempos
em tempos.

>leandro@leandro:~$ **sudo systemctl disable fwupd-refresh.service**  
>[sudo] senha para leandro:   
>leandro@leandro:~$ **sudo systemctl disable fwupd-refresh.timer**  
>Removed /etc/systemd/system/timers.target.wants/fwupd-refresh.timer.  

Os dois comandos desabilitam o serviço (não vão rodar no boot) mas ainda estão ativos. Se quiser
desabilitar imediatamente pode fazer:

> leandro@leandro:~$ **sudo systemctl stop fwupd-refresh.timer**

Se houver um **socket** associado ao serviço é importante desabilitar ele também, do contrário outro serviço pode ativá-lo
de forma indesejada.

Só para lembrar que no meu sistema toda e qualquer atualização ou instalação é feita manualmente. Portanto eu vou desabilitar
toda atualização automática de qualquer coisa.

## systemd-analyze

O comando **systemd-analyze** pode verificar a performance no tempo de boot do sistema. Ou seja, o tempo
de carregamento.

O systemd-analyze, pelo próprio nome, é um recurso do SystemD, portanto, só funcionará em sistemas onde
este sistema de init estiver instalado. 

Seu uso mais simples(sem parâmetros) retorna:

>leandro@leandro:~$ **systemd-analyze**  
>Startup finished in 4.510s (firmware) + 12.121s (loader) + 5.068s (kernel) + 55.290s (userspace) = 1min 16.990s   
>graphical.target reached after 55.098s in userspace  

O boot é dividido em quatro seções: firmware, loader, kernel e userspace.

Comparando o tempo em cada seção com o tempo total de boot podemos descobrir onde está o gargalo do sistema.

|Momento | Tempo  | % |
| :---: | :---: | :---: |
|firmware  | 4.510s  | 5,9% |
|loader    | 12.121s | 15,9% |
|kernel    |5.068s   | 6,6% |
|userspace |55.290s  | 72,7% |

Passando o parâmetro **blame**, recebemos uma listagem ordenada do maior para o menor tempo de 
carregamento dos serviços. Isso pode indicar quais serviços desabilitar para diminuir o tempo de boot
do sistema se estiver elevado.

>leandro@leandro:~$ **systemd-analyze blame**  
>2min 57.386s apt-daily.service  
>     26.027s dev-sda6.device  
>     20.651s udisks2.service  
>     15.397s networkd-dispatcher.service  
>     14.800s systemd-journal-flush.service  
>     13.618s containerd.service  
>     10.522s NetworkManager-wait-online.service  
>     10.408s accounts-daemon.service  
>      7.077s NetworkManager.service  
>     (...)

No meu caso **apt-daily.service** está levando um tempo absurdo para carregar. Se eu rodar o mesmo
comando do tópico sobre **systemctl** eu obteria:

>leandro@leandro:~$ **systemctl list-units| grep apt-daily**  
>  apt-daily.timer         loaded active waiting   Daily apt download activities

Serviços que iniciem com **apt** são relacionados à atualização do sistema. Desabilitar ou não depende
de vários fatores, mas no meu caso eu prefiro desabilitar porque eu faço toda atualização de sistema
manualmente.

Olhando novamente a listagem anterior, o **apt-daily.service** não aparece ! Por isso é melhor aperfeiçoar a busca:

>leandro@leandro:~$ **systemctl list-unit-files| grep apt-daily**  
>apt-daily-upgrade.service &nbsp; masked          enabled  
>apt-daily.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; static          enabled  
>apt-daily-upgrade.timer &nbsp; enabled         enabled  
>apt-daily.timer  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  

O resultado acima mostra uma coisa peculiar: Embora **apt-daily.service** esteja desabilitado (static),
o job **apt-daily.timer** não está! Então, mesmo que o serviço esteja desabilitado, ele ainda pode
ser lançado no boot se houver um **timer** ou um **socket** ativo.

Eu poderia configurar para os serviços relacionados ao apt carregarem durante o boot mas após a interface gráfica, por 
exemplo, ou algum tempo depois da conexão de rede, mas aqui eu vou fazer a coisa simples - no popular - Vou remover!

Vamos desabilitar tudo então:

>leandro@leandro:~$ **sudo systemctl disable apt-daily-upgrade.timer**  
>[sudo] senha para leandro:   
>Removed /etc/systemd/system/timers.target.wants/apt-daily-upgrade.timer.  
>leandro@leandro:~$ sudo systemctl disable apt-daily.timer  
>Removed /etc/systemd/system/timers.target.wants/apt-daily.timer.  
>  
>leandro@leandro:~$ **systemctl list-unit-files| grep apt-daily**  
>apt-daily-upgrade.service &nbsp;masked          enabled  
>apt-daily.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; static          enabled  
>apt-daily-upgrade.timer &nbsp;                    disabled        enabled  
>apt-daily.timer &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;     disabled        enabled   

Agora seria reiniciar a máquina e verificar o tempo de boot. Rodamos systemd-analyze de novo e verificamos
se podemos melhorar o tempo de carregamento desabilitando serviços (nem sempre será possível).

Outra opção do systemd-analyze é o parâmetro critical-chain, que nos fornece o caminho crítico,a 
sequência de serviço que mais pesa na inicialização. Assim, por exemplo:

>leandro@leandro:~$ **systemd-analyze critical-chain**  
>The time when unit became active or started is printed after the "@" character.  
>The time the unit took to start is printed after the "+" character.  
>  
>graphical.target @55.098s  
>&nbsp;└─udisks2.service @34.446s +20.651s  
>&nbsp;&nbsp;└─basic.target @33.723s  
>&nbsp;&nbsp;&nbsp;└─sockets.target @33.723s  
>&nbsp;&nbsp;&nbsp;&nbsp;└─snapd.socket @33.719s +3ms  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─sysinit.target @33.628s  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─snapd.apparmor.service @33.158s +468ms  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─apparmor.service @31.182s +1.972s  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─local-fs.target @31.179s  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─boot-efi.mount @31.033s +145ms  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─systemd-fsck@dev-disk-by\x2duuid-5C52\x2d66F4.service @30.607s +382ms  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─dev-disk-by\x2duuid-5C52\x2d66F4.device @30.604s  

No relatório acima, o momento de inicialização segue após o sinal de arroba(@) e o tempo gasto no
carregamento segue após o sinal mais(+). Assim, **udisks2.service** gastou 20.651s e iniciou 34.446s
depois do boot. Isso quase dá o tempo do processo **graphical.target** (que é o ambiente gráfico).

Pelo relatório acima, eu posso desabilitar o serviço **snapd.socket** que está relacionado ao 
gerenciamento de pacotes snap no sistema Ubuntu. 

Isso apenas no meu caso porque eu tenho a premissa de não instalar nada via pacotes snap (apesar de haver alguns na listagem abaixo). 

Eu sou conversavor e gosto de usar apenas pacotes **.deb**. Seu caso pode ser diferente de acordo com seu perfil!

>leandro@leandro:~$ **systemctl list-unit-files |grep snapd**  
>snap-snapd-16010.mount &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  
>snap-snapd-16292.mount &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  
>snapd.apparmor.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  
>snapd.autoimport.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  
>snapd.core-fixup.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  
>snapd.failure.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; static          enabled  
>snapd.recovery-chooser-trigger.service &nbsp;    enabled         enabled  
>snapd.seeded.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; disabled        enabled  
>snapd.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; disabled        enabled  
>snapd.snap-repair.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; static          enabled  
>snapd.system-shutdown.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  
>snapd.socket &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;enabled        enabled  
>snapd.snap-repair.timer &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; enabled         enabled  

>leandro@leandro:~$ **sudo systemctl disable snapd.socket snap-snapd-16010.mount snap-snapd-16292.mount snapd.apparmor.service snapd.autoimport.service snapd.core-fixup.service  snapd.recovery-chooser-trigger.service snapd.system-shutdown.service snapd.socket snapd.snap-repair.timer**  
>Removed /etc/systemd/system/multi-user.target.wants/snapd.core-fixup.service.  
>Removed /etc/systemd/system/multi-user.target.wants/snapd.apparmor.service.  
>Removed /etc/systemd/system/multi-user.target.wants/snapd.recovery-chooser-trigger.service.  
>Removed /etc/systemd/system/multi-user.target.wants/snapd.autoimport.service.  
>Removed /etc/systemd/system/multi-user.target.wants/snap-snapd-16292.mount.  
>Removed /etc/systemd/system/multi-user.target.wants/snap-snapd-16010.mount.  
>Removed /etc/systemd/system/timers.target.wants/snapd.snap-repair.timer.  
>Removed /etc/systemd/system/final.target.wants/snapd.system-shutdown.service.  
>Removed /etc/systemd/system/sockets.target.wants/snapd.socket.  

Podemos ainda obter uma representação gráfica do boot rodando:

> leandro@leandro:~$ **systemd-analyze plot > teste.png**

Isso vai gerar um relatório em gráfico de barras e salvar o conteúdo no arquivo teste.png dentro da pasta atual.

Temos também a opção de ver o caminho crítico do sistema, ou seja, a coligação de serviços dependentes responsável pelo
maior tempo de carregamento no boot.

Para obter esse relatório rodamos **systemd-analyze critical-chain**.

Podemos perceber que o serviço udisks2 está levando mais de 20 segundos para subir. Podemos dar uma olhada nele:

>leandro@leandro:~$ **systemctl status udisks2**  
>● udisks2.service - Disk Manager  
>     Loaded: loaded (/lib/systemd/system/udisks2.service; enabled; vendor preset: enabled)  
>     Active: active (running) since Mon 2022-07-25 10:09:23 -03; 10min ago  
>       Docs: man:udisks(8)  
>   Main PID: 660 (udisksd)  
>      Tasks: 5 (limit: 4488)  
>     Memory: 7.2M  
>     CGroup: /system.slice/udisks2.service  
>             └─660 /usr/lib/udisks2/udisksd  
>  
>jul 25 10:08:50 leandro systemd[1]: Starting Disk Manager...  
>jul 25 10:08:52 leandro udisksd[660]: udisks daemon version 2.8.4 starting  
>jul 25 10:08:58 leandro udisksd[660]: **failed to load module mdraid: libbd_mdraid.so.2: cannot open shared object file: No such file or directory**  
>jul 25 10:08:58 leandro udisksd[660]: **Failed to load the 'mdraid' libblockdev plugin**  
>jul 25 10:09:23 leandro systemd[1]: Started Disk Manager.  
>jul 25 10:09:23 leandro udisksd[660]: Acquired the name org.freedesktop.UDisks2 on the system message bus  

Há duas mensagens de erro na inicialização do serviço, as duas reclamando sobre um módulo mdraid. Vamos corrigir!

Como o nome já indica, esse é um conjunto de utilitários e módulos para tratar de discos RAID. 

Antes de decidir se removo ou não, deixe-me ver se a biblioteca libbd_mdraid.so.2 existe no meu
sistema:

>leandro@leandro:~$ **ldconfig -p| grep libbd_mdraid.so.2**  
>leandro@leandro:~$  

Aparentemente não. Então deixe-me ver de qual pacote essa biblioteca faz parte:

>leandro@leandro:~$ **apt-file search libbd_mdraid.so.2**  
>libblockdev-mdraid2: /usr/lib/x86_64-linux-gnu/libbd_mdraid.so.2  
>libblockdev-mdraid2: /usr/lib/x86_64-linux-gnu/libbd_mdraid.so.2.0.0  

Essa biblioteca faz parte do pacote **libblockdev-mdraid2**. Deixe-me ver se esse pacote está 
instalado em minha máquina:

>leandro@leandro:~$ **apt-cache policy libblockdev-mdraid2**  
>libblockdev-mdraid2:  
>  **Instalado: (nenhum)**  
>  Candidato: 2.23-2ubuntu3  
>  Tabela de versão:  
>     2.23-2ubuntu3 500  
>        500 http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  

Não está instalado. Instalaremos então:

>leandro@leandro:~$ **sudo apt install libblockev-crypto2 libblockdev-mdraid2**

Reiniciando e verificando:

>leandro@leandro:~$ **systemd-analyze**  
>Startup finished in 3.443s (firmware) + 11.601s (loader) + 5.597s (kernel) + 40.539s (userspace) = 1min 1.181s   
>graphical.target reached after 39.760s in userspace  

Houve já uma redução de cerca de 20% no tempo de boot. Há ainda mais algumas considerações: Como eu desabilitei os serviços
do snap (não uso) eu também vou desabilitar os dispositivos de loop que ele usa:

>leandro@leandro:~$ **df**  
>Sist. Arq.     Blocos de 1K    Usado Disponível Uso% Montado em  
>udev &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;               1914964        0    1914964   0% /dev  
>tmpfs &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;               390716     1520     389196   1% /run  
>/dev/sda6 &nbsp;&nbsp;&nbsp; 87178268 44907620   37796232  55% /  
>tmpfs &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;              1953564        0    1953564   0% /dev/shm  
>tmpfs &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;                  5120        4       5116   1% /run/lock  
>tmpfs &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;              1953564        0    1953564   0% /sys/fs/cgroup  
> /dev/loop0 &nbsp;&nbsp; 56960    56960          0 100% /snap/core18/2344   
>/dev/loop4 &nbsp;&nbsp; 63488    63488          0 100% /snap/core20/1518  
>/dev/loop1 &nbsp;&nbsp;            128      128          0 100% /snap/bare/5  
>/dev/loop5 &nbsp;&nbsp;          168832   168832          0 100% /snap/gnome-3-28-1804/161  
>/dev/loop2 &nbsp;&nbsp;           87680    87680          0 100% /snap/compress-video/23  
>/dev/loop3 &nbsp;&nbsp;           56960    56960          0 100% /snap/core18/2409  
>/dev/loop8 &nbsp;&nbsp;         599040   599040          0 100% /snap/vlc/3008  
>/dev/loop7 &nbsp;&nbsp;          93952    93952          0 100% /snap/gtk-common-themes/1535  
>/dev/loop6 &nbsp;&nbsp;           83328    83328          0 100% /snap/gtk-common-themes/1534  
>/dev/sda1 &nbsp;&nbsp;&nbsp;           497696     5356     492340   2% /boot/efi  
>tmpfs &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;                390712       16     390696   1% /run/user/1000  

Para isso vou remover o pacote snap (lembrando que eu não uso snap - apesar de ter instalado alguns programas para testar uma coisa ou outra):

> leandro@leandro:~$ **sudo apt purge snapd**


>leandro@leandro:~$ **df**  
>Sist. Arq.     Blocos de 1K    Usado Disponível Uso% Montado em  
>udev                1914964        0    1914964   0% /dev  
>tmpfs                390716     1520     389196   1% /run  
>/dev/sda6          87178268 43253432   39450420  53% /  
>tmpfs               1953564        0    1953564   0% /dev/shm  
>tmpfs                  5120        4       5116   1% /run/lock  
>tmpfs               1953564        0    1953564   0% /sys/fs/cgroup  
>/dev/sda1            497696     5356     492340   2% /boot/efi  
>tmpfs                390712       48     390664   1% /run/user/1000  

Verificando novamente o tempo de boot:

>leandro@leandro:~$ **systemd-analyze**  
>Startup finished in 3.104s (firmware) + 11.697s (loader) + 6.363s (kernel) + 35.386s (userspace) = 56.553s   
>graphical.target reached after 35.359s in userspace  

Meu tempo de boot agora diminuiu 25%! Por isso é importante verificar as mensagens de erro no sistema:
elas podem significar diminuição no tempo de boot, principalmente quando o sistema tenta iniciar
um serviço e não consegue porque todas as unidades estão encadeadas e a demora em um serviço pode
se propagar mais para a frente com a demora em outro.

Agora um último truque. Baseando em meu novo relatório de 

> leandro@leandro:~$ **systemd-analyze blame**  
>19.245s udisks2.service  
>14.045s networkd-dispatcher.service  
>12.662s containerd.service  
>(..)

eu tenho 71 serviços ativos.

>leandro@leandro:~$ **systemd-analyze blame --no-pager| wc -l**  
>**71**  

E que tal se eu otimizasse meu tempo de busca e utilizasse o princípio de Paretto aqui e analisasse apenas os principais consumidores de tempo??

Pelo princípio de Paretto aproximadamente 20% dos serviços consomem 80% do tempo de carregamento. Como eu tenho
71 serviços eu devo me preocupar com mais ou menos 14 serviços. Como o systemd-analyze já me fornece
a lista ordenada do maior para o menor, eu posso obter a lista rodando:

>leandro@leandro:~$ **systemd-analyze blame --no-pager| head -n 14**  
>19.245s udisks2.service  
>14.045s networkd-dispatcher.service  
>12.662s containerd.service  
>10.168s accounts-daemon.service  
> 9.845s NetworkManager-wait-online.service  
> 8.546s systemd-journal-flush.service  
> 7.952s dev-sda6.device  
> 6.431s NetworkManager.service  
> 6.350s avahi-daemon.service  
> 6.333s polkit.service  
> 5.799s thermald.service  
> 5.792s systemd-logind.service  
> 5.791s wpa_supplicant.service  
> 5.277s lightdm.service  

Nesta nova listagem, há três serviços que estão com problemas (usei systemctl status para verificar): networkd-dispatcher, thermald e wpa_supplicant.

Quais desses serviços eu poderia desabilitar à primeira vista? Containerd e networkd-dispatcher. 

Containerd é o docker que está instalado na minha máquina. Como é uma máquina de desenvolvimento, não preciso dele carregando durante o boot.

Portanto vou desabilitá-lo e carregar manualmente quando eu for usar:

>leandro@leandro:~$ **sudo systemctl disable containerd**  
>[sudo] senha para leandro:   
>Removed /etc/systemd/system/multi-user.target.wants/containerd.service.  
>leandro@leandro:~ $ sudo systemctl disable networkd-dispatcher  
>Removed /etc/systemd/system/multi-user.target.wants/networkd-dispatcher.service.  

O outro é o networkd_dispatcher que define configurações de rede, mas que usa systemd-networkd. No meu caso já tinha desabilitado
o systemd-networkd, ou seja, o networkd_dispatcher nunca vai gerenciar nada!

>leandro@leandro:~$ **systemctl status systemd-networkd**  
>● systemd-networkd.service - Network Service  
>     Loaded: loaded (/lib/systemd/system/systemd-networkd.service; disabled; vendor preset: enabled)  
>     Active: inactive (dead)  
>       Docs: man:systemd-networkd.service(8)  

Thermald e wpa_supplicant eu pretendo analisar em uma eventual parte 02 desta dica (que já está ficando extensa).

Reiniciando e verificando o tempo:

>leandro@leandro:~$ **systemd-analyze**  
>Startup finished in 3.098s (firmware) + 4.743s (loader) + 5.830s (kernel) + 35.412s (userspace) = 49.084s   
>graphical.target reached after 35.382s in userspace  


Chegamos assim a uma melhoria de cerca de 36%! Reduzimos o tempo de boot em 1/3!  Mas isso não é o fim. Bem, é por enquanto.

# Conclusão

O objetivo desta dica é mostrar como encontrar possíveis erros no sistema operacional Ubuntu Linux que possam
vir a comprometer a performance ou a estabilidade do funcionamento.

Vimos o comando **dmesg**, o **systemd-analyze** e o **systemctl** e analisamos diversas saídas destes comandos
via terminal.

No final acabamos nos concentrando em melhorar a performance de boot do sistema desabilitando serviços,
instalando e desinstalando pacotes desnecessários.

Infelizmente esta dica estava ficando muito grande (ficou maior do que eu havia planejado quando
comecei) por isso dividi em partes. Ainda falta analisar as saídas do **journalctl** e os arquivos de 
log relacionandos ao sistema gráfico.
