---
title: Segurança em camadas com SystemD
date: 2022-10-02
tags:
  - "ubuntu"
  - "linux"
  - "systemD"
categories:
  - "Linux"
  - "Artigos"
---
Existe um aspecto muito pouco conhecido do SystemD que é a capacidade de obter dados estruturados para diagnosticar problemas.

A ferramenta systemd-analyze é muito pouco conhecida e, de sua potencialidade total, muito pouco é efetivamente usado.

Além de permitir diagnosticar problemas relacionados à inicialização do sistema, o SystemD fornece mecanismos para avaliar e corrigir
a (in)segurança dos serviços(daemons) executando sob ele, criando uma camada de proteção.
<!--more-->

A primeira camada de proteção é feita pelo próprio serviço ou aplicação. Sua configuração e codificação.

Se um atacante explorar uma falha, é importante que possamos limitar ao máximo o estrago que ele possa fazer restringindo os recursos
disponíveis.

Eu já analizei o systemd-analyze em outro artigo sobre [como descobrir erros no Linux](/dicas/procurando-erros-no-linux_01/).

É uma excelente ferramenta, mas além da capacidade de diagnosticar problemas relacionados aos serviços durante a inicialização, 
esse comando também permite avaliar a segurança de acordo com um checklist de itens!

Rodando **systemd-analyze security** podemos fazer uma auditoria de todos os serviços.

>leandro@leandro:~$ **systemd-analyze security**  
>UNIT                                  EXPOSURE PREDICATE HAPPY  
>&nbsp;ModemManager.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 5.8 MEDIUM    😐    
>&nbsp;NetworkManager.service &nbsp;&nbsp;&nbsp;&nbsp; 7.8 EXPOSED   🙁    
>&nbsp;accounts-daemon.service &nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;acpid.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;9.6 UNSAFE    😨    
>&nbsp;alsa-state.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; 9.6 UNSAFE    😨    
>&nbsp;anacron.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;apport.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;avahi-daemon.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;cron.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;cups-browsed.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;cups.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;dbus.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;dmesg.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;emergency.service  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  9.5 UNSAFE    😨    
>&nbsp;getty@tty1.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; 9.6 UNSAFE    😨    
>&nbsp;getty@tty7.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; 9.6 UNSAFE    😨    
>&nbsp;hddtemp.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;irqbalance.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; 6.1 MEDIUM    😐    
>&nbsp;kerneloops.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; 9.2 UNSAFE    😨    
>&nbsp;lightdm.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;ondemand.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;plymouth-start.service &nbsp;&nbsp;&nbsp;&nbsp;  9.5 UNSAFE    😨    
>&nbsp;polkit.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;rc-local.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;rescue.service&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.5 UNSAFE    😨    
>&nbsp;rsync.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;rsyslog.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;rtkit-daemon.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  7.1 MEDIUM    😐    
>&nbsp;smartmontools.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;systemd-ask-password-console.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.3 UNSAFE    😨    
>&nbsp;systemd-ask-password-plymouth.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;9.5 UNSAFE    😨    
>&nbsp;systemd-ask-password-wall.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp; 9.4 UNSAFE    😨    
>&nbsp;systemd-fsckd.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.5 UNSAFE    😨    
>&nbsp;systemd-initctl.service &nbsp;&nbsp;&nbsp; 9.3 UNSAFE    😨    
>&nbsp;systemd-journald.service &nbsp;&nbsp; 4.4 OK        🙂    
>&nbsp;systemd-logind.service &nbsp;&nbsp;&nbsp;&nbsp; 2.8 OK        🙂    
>&nbsp;systemd-networkd.service &nbsp;&nbsp; 3.1 OK        🙂    
>&nbsp;systemd-resolved.service &nbsp;&nbsp;  2.2 OK        🙂    
>&nbsp;systemd-rfkill.service &nbsp;&nbsp;&nbsp;&nbsp;  9.3 UNSAFE    😨    
>&nbsp;systemd-timesyncd.service &nbsp; 2.1 OK        🙂    
>&nbsp;systemd-udevd.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 8.4 EXPOSED   🙁    
>&nbsp;thermald.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨    
>&nbsp;udisks2.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;unattended-upgrades.service 9.6 UNSAFE    😨    
>&nbsp;upower.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  2.3 OK        🙂    
>&nbsp;user@1000.service  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;9.4 UNSAFE    😨    
>&nbsp;uuidd.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  4.5 OK        🙂    
>&nbsp;whoopsie.service &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;  9.6 UNSAFE    😨    
>&nbsp;wpa_supplicant.service &nbsp;&nbsp;&nbsp;&nbsp; 9.6 UNSAFE    😨  

Obtemos um relatório como acima, dividido em 04 colunas, todas fornecendo a mesma informação, mas em um formato diferente.

Na primeira coluna temos o nome da **Unit**; na segunda coluna temos o grau de exposição, que é uma métrica calculada verificando
se as configurações do serviço estão de acordo com um checklist de segurança; a terceira coluna traduz o valor do grau de 
exposição em forma verbal, onde podemos ter avaliações como **UNSAFE**, **OK**, **EXPOSED**, **MEDIUM**; o último campo facilita ainda
mais a compreensão ao traduzir o grau de exposição em forma pictórica, isto é, em um emoji, onde podemos ter uma carinha feliz quando 
o serviço está protegido e uma carinha triste quando o serviço está exposto.

É importante mencionar que o grau de exposição varia entre 0 e 10, sendo 0(zero) considerado o mais seguro possível
e 10(dez) o mais inseguro possível considerando os itens da checklist de verificações que o systemd-analyze vai analisar.

Ou seja, o Systemd pretende fazer a segurança do sistema mensurável e demonstrável!!!! Esse paradigma é herdado da filosofia da ciência,
onde só pode ser melhorado aquilo que pode ser medido!

Nosso relatório acima mostra que alguns serviços estão com nível aceitável de segurança, como o **systemd-logind**. Outros, como
o **cups-browsed** estão com um nível preocupante.

Além disso, o systemd-analyze pode mostrar como ele chegou a essa avaliação, isto é, quais critérios ele usou para chegar a este valor
e como calculou a avaliação final. Para isso, basta rodar o comando novamente passando o nome da **Unit** como segundo argumento e poderemos
ver os itens de checklist de segurança para este serviço(daemon):

>leandro@leandro:~$ **systemd-analyze security cups --no-pager**  
>&nbsp;**NAME**                                                        **DESCRIPTION**                                                            **EXPOSURE**  
>✗ PrivateNetwork=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has access to the host's network                                     0.5  
>✗ User=/DynamicUser=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service runs as root user                                                    0.4  
>✗ CapabilityBoundingSet=~CAP_SET(UID|GID|PCAP)&nbsp;Service may change UID/GID identities/capabilities                           0.3  
>✗ CapabilityBoundingSet=~CAP_SYS_ADMIN&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has administrator privileges                                         0.3  
>✗ CapabilityBoundingSet=~CAP_SYS_PTRACE&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has ptrace() debugging abilities                                     0.3  
>✗ RestrictAddressFamilies=~AF_(INET|INET6)&nbsp;&nbsp;&nbsp;&nbsp; Service may allocate Internet sockets                                        0.3  
>✗ RestrictNamespaces=~CLONE_NEWUSER&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create user namespaces                                           0.3  
>✗ RestrictAddressFamilies=~… &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may allocate exotic sockets                                          0.3  
>✗ CapabilityBoundingSet=~CAP_(CHOWN|FSETID|SETFCAP) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may change file ownership/access mode/capabilities unrestricted      0.2  
>✗ CapabilityBoundingSet=~CAP_(DAC_*|FOWNER|IPC_OWNER)&nbsp;&nbsp;&nbsp;&nbsp; Service may override UNIX file/IPC permission checks                         0.2  
>✗ CapabilityBoundingSet=~CAP_NET_ADMIN &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service has network configuration privileges                                 0.2  
>✗ CapabilityBoundingSet=~CAP_RAWIO &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service has raw I/O access                                                   0.2  
>✗ CapabilityBoundingSet=~CAP_SYS_MODULE &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service may load kernel modules                                              0.2  
>✗ CapabilityBoundingSet=~CAP_SYS_TIME&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service processes may change the system clock                                0.2  
>✗ DeviceAllow=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service has no device ACL                                                    0.2  
>✗ IPAddressDeny=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not define an IP address whitelist                              0.2  
>✓ KeyringMode=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service doesn't share key material with other services                          
>✗ NoNewPrivileges=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service processes may acquire new privileges                                 0.2  
>✓ NotifyAccess=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service child processes cannot alter service state                              
>✗ PrivateDevices=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service potentially has access to hardware devices                           0.2  
>✗ PrivateMounts=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may install system mounts                                            0.2  
>✗ PrivateTmp=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has access to other software's temporary files                       0.2  
>✗ PrivateUsers=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has access to other users                                            0.2  
>✗ ProtectClock=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may write to the hardware clock or system clock                      0.2  
>✗ ProtectControlGroups=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may modify the control group file system                             0.2  
>✗ ProtectHome=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has full access to home directories                                  0.2  
>✗ ProtectKernelLogs=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may read from or write to the kernel log ring buffer                 0.2  
>✗ ProtectKernelModules=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may load or read kernel modules                                      0.2  
>✗ ProtectKernelTunables=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may alter kernel tunables                                            0.2  
>✗ ProtectSystem=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has full access to the OS file hierarchy                             0.2  
>✗ RestrictAddressFamilies=~AF_PACKET&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may allocate packet sockets                                          0.2  
>✗ RestrictSUIDSGID=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create SUID/SGID files                                           0.2  
>✗ SystemCallArchitectures=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may execute system calls with all ABIs                               0.2  
>✗ SystemCallFilter=~@clock&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2   
>✗ SystemCallFilter=~@debug&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@module&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@mount&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@raw-io&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@reboot&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@swap&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@privileged&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✗ SystemCallFilter=~@resources&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.2  
>✓ AmbientCapabilities=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service process does not receive ambient capabilities                            
>✗ CapabilityBoundingSet=~CAP_AUDIT_*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has audit subsystem access                                           0.1  
>✗ CapabilityBoundingSet=~CAP_KILL&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may send UNIX signals to arbitrary processes                         0.1  
>✗ CapabilityBoundingSet=~CAP_MKNOD&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create device nodes                                              0.1  
>✗ CapabilityBoundingSet=~CAP_NET_(BIND_SERVICE|BROADCAST|RAW)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service has elevated networking privileges                                   0.1  
>✗ CapabilityBoundingSet=~CAP_SYSLOG&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service has access to kernel logging                                         0.1  
>✗ CapabilityBoundingSet=~CAP_SYS_(NICE|RESOURCE) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Service has privileges to change resource use parameters                     0.1   
>✗ RestrictNamespaces=~CLONE_NEWCGROUP&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create cgroup namespaces                                         0.1  
>✗ RestrictNamespaces=~CLONE_NEWIPC&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create IPC namespaces                                            0.1  
>✗ RestrictNamespaces=~CLONE_NEWNET&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create network namespaces                                        0.1  
>✗ RestrictNamespaces=~CLONE_NEWNS&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create file system namespaces                                    0.1  
>✗ RestrictNamespaces=~CLONE_NEWPID&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create process namespaces                                        0.1  
>✗ RestrictRealtime=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may acquire realtime scheduling                                      0.1  
>✗ SystemCallFilter=~@cpu-emulation&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.1  
>✗ SystemCallFilter=~@obsolete&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not filter system calls                                         0.1  
>✗ RestrictAddressFamilies=~AF_NETLINK&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may allocate netlink sockets                                         0.1  
>✗ RootDirectory=/RootImage=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service runs within the host's root directory                                0.1  
>  SupplementaryGroups=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service runs as root, option does not matter                                      
>✗ CapabilityBoundingSet=~CAP_MAC_*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may adjust SMACK MAC                                                 0.1  
>✗ CapabilityBoundingSet=~CAP_SYS_BOOT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may issue reboot()                                                   0.1  
>✓ Delegate=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service does not maintain its own delegated control group subtree                 
>✗ LockPersonality=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may change ABI personality                                           0.1  
>✗ MemoryDenyWriteExecute=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create writable executable memory mappings                       0.1  
>  RemoveIPC=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service runs as root, option does not apply                                      
>✗ RestrictNamespaces=~CLONE_NEWUTS&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create hostname namespaces                                       0.1  
>✗ UMask=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Files created by service are world-readable by default                       0.1  
>✗ CapabilityBoundingSet=~CAP_LINUX_IMMUTABLE&nbsp;&nbsp;&nbsp;Service may mark files immutable                                             0.1  
>✗ CapabilityBoundingSet=~CAP_IPC_LOCK&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may lock memory into RAM                                             0.1  
>✗ CapabilityBoundingSet=~CAP_SYS_CHROOT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may issue chroot()                                                   0.1  
>✗ ProtectHostname=&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may change system host/domainname                                    0.1  
>✗ CapabilityBoundingSet=~CAP_BLOCK_SUSPEND&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may establish wake locks                                             0.1  
>✗ CapabilityBoundingSet=~CAP_LEASE&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may create file leases                                               0.1  
>✗ CapabilityBoundingSet=~CAP_SYS_PACCT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may use acct()                                                       0.1  
>✗ CapabilityBoundingSet=~CAP_SYS_TTY_CONFIG&nbsp;&nbsp;&nbsp;&nbsp;Service may issue vhangup()                                                  0.1  
>✗ CapabilityBoundingSet=~CAP_WAKE_ALARM&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may program timers that wake up the system                           0.1  
>✗ RestrictAddressFamilies=~AF_UNIX&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Service may allocate local sockets                                           0.1  
>   
>→ **Overall exposure level for cups.service: 9.6 UNSAFE 😨**  

Podemos ver que se trata de um checklist onde temos quatro colunas: A primeira coluna (quase invisível), mostra se o item está
presente ou ausente, ou seja, se passou ou não. A segunda coluna corresponde ao nome da diretiva de configuração, mais detalhes a seguir. A terceira
coluna dá uma descrição sucinta da configuração que deveria ser evitada. A quarta coluna dá grau de exposição correspondente à ausência
deste item de configuração. O valor corresponde a um peso ajustado de acordo com o grau de comprometimento de segurança que pode provocar.
A interpretação do cálculo é de que o sistema inicia com o valor de 10(dez) e cada item configurado corretamente tem o seu peso associado
decrementado e um ícone de seta na primeira coluna apontando esse item como OK.

Como mencionado, cada item desse relatório corresponde a uma diretiva específica no arquivo de configuração da Unit.

No primeiro relatório, vários serviços são marcados como inseguros (UNSAFE). Isso acontece por que nem todas as aplicações usam
as funcionalidades fornecidas pelo SystemD.

A quantidade de serviços marcados como inseguros certamente vai assustar quem o vir pela primeira vez, por isso é necessário
entender que a métrica é calculada indistintamente para todos os serviços, ou seja, tanto um servidor web quanto o plymouth, que mostra 
a logomarca da distribuição no início do boot, por exemplo, são avaliados.

Portanto pode ser custoso conduzir todos os serviços chegarem à avaliação OK. É necessário, então, algum método que permita
alcançar a máxima segurança com o mínimo de esforço. 

Então, deveríamos começar analizando os serviços que estejam expostos à Internet ou processem dados não confiáveis que possam
ser fornecidos pelo usuário, como servidores Web, servidores de bancos de dados, servidores de VPN entre outros.

Sem mencionar, é claro, os serviços que nem deveriam estar habilitados num servidor, como wpa_supplicant e lightdm entre outros.
Cada um uma possível porta de entrada para um invador.

## Protegendo

Agora, dado que obtivemos uma avaliação da segurança do nosso sistema sob o ponto de vista do SystemD, como podemos melhorar?

Systemd permite que os serviços(Unit) rodem dentro de um ambiente protegido (uma "sandbox") fornecidos pelo kernel Linux. Quem pensou
num contâiner quase acertou.

Assim como um contâiner, o kernel Linux pode filtrar e limitar acesso a sistemas de arquivos, redes e dispositivos entre outras
coisas. Ou seja, permite isolar um serviço do restante do sistema usando namespaces dentro do espaço do kernel.

Para ajustar a segurança de um serviço devemos adotar uma estratégia incremental. Ativar um item de configuração, recarregar o serviço, ver se tudo continua
funcionando conforme o esperado e seguir para o próximo item até alcançar uma nível de segurança considerado aceitável.

Como exemplo, vamos considerar o serviço do cups do último relatório e configurar algumas coisas para aumentar sua proteção. 

Para o artigo não ficar muito longo vou configurar somente algumas diretivas. 

São as que eu considero essenciais, mas não entenda como um ajuste exaustivo. Para mais detalhes siga as referências no final. 


### 1 - Evitar que um atacante escale privilégios

Se um atacante explorar alguma vulnerabilidade, eu não quero que ele se torne root, pois uma vez alcançado o acesso root é fim de jogo.

Para começar vamos recordar nosso grau de exposição:

> leandro@leandro:/usr/lib/systemd/system$ **systemd-analyze security cups --no-pager| grep exposure**  
> → Overall exposure level for cups.service: **9.6 UNSAFE** 😨  

A Unit do cups está em /usr/lib/systemd/system/cups.service. O conteúdo atual é o seguinte:
```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```

Vamos adicionar a diretiva **NoNewPrivileges=true** dentro da seção **[Service]**. Vai ficar assim:

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```

Recarregamos todos os serviços:

> leandro@leandro:/usr/lib/systemd/system$ **sudo systemctl daemon-reload**

E verificamos o grau de exposição:

>leandro@leandro:/usr/lib/systemd/system$ **systemd-analyze security cups --no-pager| grep exposure**  
>→ Overall exposure level for cups.service: **9.4 UNSAFE** 😨  

Veja que uma grande proteção diminuiu muito pouco, mas já é um começo. Uma outra estratégia poderia ser verificar quais fatores possuem
os maiores graus de exposição e começar por eles.

### 2- Evite gente xeretando o /tmp

Uma vez tendo explorada uma vulnerabilidade eu não quero que um atacante possa ver acesso a dados de outros processos. Vários aplicativos
criam arquivos temporários durante seu tempo de existência e o lugar padrão para eles é o diretório /tmp do sistema.

Voltamos ao arquivo **/usr/lib/systemd/system/cups.service**  e vamos adicionar a diretiva **PrivateTmp=yes**. Vai ficar assim:
```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```

>leandro@leandro:/usr/lib/systemd/system$ **sudo systemctl daemon-reload**  
>leandro@leandro:/usr/lib/systemd/system$ **systemd-analyze security cups --no-pager| grep exposure**  
>→ Overall exposure level for cups.service: **9.0 UNSAFE** 😨  

Veja que eliminamos dois problemas, mas o systemD não ligou a mínima! Ainda estamos com um nível insatisfatório de segurança.

### 3 - Torne variáveis do kernel somente leitura

Agora seguiremos para configurações mais sutis. 

Uma vez que um atacante alcança o sistema, a primeira coisa que ele fará será tentar garantir que vai continuar acessando o sistema.

Ele pode conseguir isso de diversas maneiras e uma delas é modificar o sistema. Em particular, um atacante não deve ser capaz
de carregar módulos de kernel, os chamados rootkits, nem modificar parâmetros de configuração, principalmente subsistemas como acpi entre outros.

Por isso vamos adicionar as diretivas **ProtectKernelTunables=yes**, **ProtectKernelModules=yes**, **ProtectControlGroups=yes**, como abaixo:

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```
E verificar o impacto:

> leandro@leandro:/usr/lib/systemd/system$ **systemd-analyze security cups --no-pager| grep exposure**  
>→ Overall exposure level for cups.service: **8.3 EXPOSED** 🙁  

Estamos abaixo de 9 pela primeira vez, e protegemos o sistema de três problemas, mesmo assim nossa avaliação ainda não é boa!

### 4 - Montar diretório /usr, /boot, /boot/efi e /etc como somente leitura

Novamente a lógica é de que um atacante não pode modificar o sistema em caso de sucesso. Queremos impedir que ele modifique o binário
de algum aplicativo inserindo uma versão maliciosa e que ele possa inserir um aplicativo efi de boot (como um grub malicioso, por exemplo). Além
disso, devemos proteger o diretório /etc de modificações visto que ele contém as configurações do sistema.

Vamos adicionar a diretiva **ProtectSystem=full**.

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
ProtectSystem=full

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```
E vericar o resultado:

>leandro@leandro:/usr/lib/systemd/system$ **sudo systemctl daemon-reload**  
>leandro@leandro:/lib/systemd/system$ **systemd-analyze security cups --no-pager| grep exposure**  
>→ Overall exposure level for cups.service: **8.2 EXPOSED** 🙁  

Uma coisa a ser mencionada é que essa configuração é para a Unit, ou seja, apenas a Unit enxerga esses diretórios como somente leitura e
é importante destacar essa informação. 

Como eu falei, essa defesa funciona em camadas, mas o systemD não é infalível e devemos configurar nossa máquina de modo a proteger
esses diretórios independente do init utilizado(adicionar mais uma camada).

### 5 - Excluir namespaces que o serviço não esteja utilizando

Eu me concentro nos problemas anteriores, a partir daqui o SystemD nos ajuda a nos proteger de problemas que nem sequer sabíamos
que estávamos expostos(pelo menos é o meu caso).

Agora vamos restringir nosso serviço de modo que ele acesse apenas o que precise para funcionar em termos de recursos de sistema como
interfaces de rede, comunicação inter processos entre outros. Eu vou configurar a diretiva **RestrictNamespaces** e dizer que meu serviço
cups só vai poder usar a interface de rede, mais nada. Por isso a lista de itens **uts**, **ipc**, **pid**, **user**, **cgroup**

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
ProtectSystem=full
RestrictNamespaces=uts ipc pid user cgroup

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```
E novamente verificar o impacto:

>leandro@leandro:/lib/systemd/system$ **sudo systemctl daemon-reload**  
>leandro@leandro:/lib/systemd/system$ **systemd-analyze security cups --no-pager| grep exposure**  
>→ Overall exposure level for cups.service: **8.0 EXPOSED** 🙁  

### 6 - Limitar as capacidades

Capacidade aqui significa o que um processo com nível não privilegiado pode fazer. Não é possível explicar em pormenores cada configuração, 
mas para mais detalhes acesse a manpage - **man capabilities** na linha de comando.

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
ProtectSystem=full
RestrictNamespaces=uts ipc pid user cgroup
CapabilityBoundingSet=CAP_NET_BIND_SERVICE CAP_DAC_READ_SEARCH

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```
Acima permitimos que o serviço rode em uma porta privilegiada.

>leandro@leandro:/lib/systemd/system$ sudo systemctl daemon-reload  
>leandro@leandro:/lib/systemd/system$ systemd-analyze security cups --no-pager| grep exposure  
>→ Overall exposure level for cups.service: **5.9 MEDIUM** 😐  

Finalmente conseguimos uma carinha indiferente, o que já é uma vitória. 

### 7 - Criar um dispositivo de rede para a Unit

Aqui a ideia é a mesma de um contâiner e isolar o serviço do restante do sistema. Para isso vamos setar **PrivateNetwork=true**

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
ProtectSystem=full
RestrictNamespaces=uts ipc pid user cgroup
CapabilityBoundingSet=CAP_NET_BIND_SERVICE CAP_DAC_READ_SEARCH
PrivateNetwork=true

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```
E verificar o impacto:

>leandro@leandro:/lib/systemd/system$ sudo systemctl daemon-reload  
>leandro@leandro:/lib/systemd/system$ systemd-analyze security cups --no-pager| grep exposure  
>→ Overall exposure level for cups.service: 5.5 MEDIUM 😐  

Tivemos algum trabalho, mais ainda temos uma carinha indiferente e uma avaliação de 5.5! Mas para mim chega, já estou satisfeito
com as configurações que eu fiz. É claro que um serviço exposto à Internet requereria mais algum esforço, mas para este artigo já está
bom.

### Um alerta

Algumas configurações podem provocar erro, por isso é necessário adotar uma estratégia passo a passo. 

Por exemplo, adicionando a diretiva **DynamicUser=true** poderíamos permitir que o sistema criasse um "usuário efêmerero" para o serviço.

Um usuário efêmero é um usuário exclusivo para rodar serviços e que não vai ter login nem acesso a shell, como o usuário **nobody** em muitos sistemas.

Abaixo a configuração:

```
[Unit]
Description=CUPS Scheduler
Documentation=man:cupsd(8)
After=sssd.service
Requires=cups.socket

[Service]
ExecStart=/usr/sbin/cupsd -l
Type=simple
Restart=on-failure
NoNewPrivileges=true
PrivateTmp=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
ProtectSystem=full
RestrictNamespaces=uts ipc pid user cgroup
CapabilityBoundingSet=CAP_NET_BIND_SERVICE CAP_DAC_READ_SEARCH
PrivateNetwork=true
DynamicUser=true

[Install]
Also=cups.socket cups.path
WantedBy=printer.target
```

Porém, se recarregarmos o serviço:
> leandro@leandro:/lib/systemd/system$ sudo systemctl daemon-reload  

E checarmos o status, veremos que temos problema:

> leandro@leandro:/lib/systemd/system$ systemctl status cups.service  
>● cups.service - CUPS Scheduler  
>&nbsp;&nbsp;     Loaded: loaded (/lib/systemd/system/cups.service; enabled; vendor preset: enabled)  
>&nbsp;&nbsp;     Active: active (running) since Tue 2022-09-13 23:27:05 -03; 2min 28s ago  
>TriggeredBy: ● cups.path  
>&nbsp;&nbsp;             ● cups.socket  
>&nbsp;&nbsp;       Docs: man:cupsd(8)  
>&nbsp;   Main PID: 8329 (cupsd)  
>&nbsp;&nbsp;      Tasks: 1 (limit: 4488)  
>&nbsp; &nbsp;    Memory: 1.7M  
>&nbsp;&nbsp;     CGroup: /system.slice/cups.service  
>&nbsp;&nbsp;             └─8329 /usr/sbin/cupsd -l  
>  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to change permissions of "/var/log/cups" - Read-only file system**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to open log file "/var/log/cups/error_log" - Permission denied**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to change permissions of "/var/log/cups" - Read-only file system**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to open log file "/var/log/cups/error_log" - Permission denied**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to change permissions of "/var/log/cups" - Read-only file system**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to open log file "/var/log/cups/access_log" - Permission denied**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to change permissions of "/var/log/cups" - Read-only file system**  
>set 13 23:27:05 leandro cupsd[8329]: **Unable to open log file "/var/log/cups/access_log" - Permission denied**  
>set 13 23:27:37 leandro cupsd[8329]: **Unable to change permissions of "/var/log/cups" - Read-only file system**  
>set 13 23:27:37 leandro cupsd[8329]: **Unable to open log file "/var/log/cups/error_log" - Permission denied**  

Nossa alteração provocou problemas no permissionamento. Por isso precisamos desfazer o que fizemos. 

É bom lembrar que nós não precisamos chegar a um valor de zero para todo serviço. O que é importante é implementarmos as proteções que nos deixem
tranquilos quanto à segurança do serviço e do sistema como um todo.

Por isso vou parar por aqui. Conseguimos passar nosso grau de exposição de 9.6 para 5.5 - quase metade!


## Conclusão

O tão mal falado quanto incompreendido SystemD fornece uma série de ferramentas que auxiliam o administrador de sistemas Linux.

Uma delas é o systemd-analyze, que além de fornecer dados sobre o carregamento do sistema, fornece também subsídios para auditar a 
segurança dos serviços executados.

O sub comando security vem com uma métrica para calcular a segurança, permitindo saber se um serviço está seguro em
termo de configuração da Unit e vem também com um checklist com itens para melhorar a proteção usando uma estratégia passo a passo, encapsulando cada serviço ao máximo
como um verdadeiro contâiner sem **Docker**.

E o melhor de tudo é que a cada passo é possível explicar o que está sendo feito e como está sendo feito!

Isso permite que, se um atacante for capaz de burlar a configuração de um serviço ou explorar uma falha de codificação, não seja
capaz de ir além dos limites que lhe impusermos, da mesma forma que, para proteger uma casa, adicionamos um muro, depois colocamos cães
de guarda, em seguida colocamos grades nas janelas e cadeados nas portas.

Um nível impedindo um agressor de prosseguir para o seguinte!

### Fontes:

https://lincolnloop.com/blog/sandboxing-services-systemd/

https://www.freedesktop.org/software/systemd/man/systemd.exec.html

https://www.ctrl.blog/entry/systemd-service-hardening.html

https://www.admin-magazine.com/Archive/2022/67/Harden-services-with-systemd


