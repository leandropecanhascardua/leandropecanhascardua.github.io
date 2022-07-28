---
title: Desfragmentando discos no Ubuntu Linux
date: 2022-06-22
tags:
  - "linux"
  - "terminal"	
  - "HD"
categories:
  - "Linux"
---
Há um mito de que sistemas Linux não precisam de desfragmentação. Será verdade?

Nossa pesquisa se resume apenas ao sistema de arquivos ext4 e não desfragmentamos discos, mas diretórios e arquivos.
<!--more-->

Para saber qual sistemas de arquivos sua instalação está usando, rode o **findmnt** ou **lsblk**
>leandro@leandro:~$ findmnt /  
>TARGET SOURCE    FSTYPE OPTIONS  
>\/      /dev/sda6 **ext4**   rw,relatime,errors=remount-ro  


>leandro@leandro:~$ lsblk -f   
>NAME   FSTYPE   LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT  
>loop0  squashfs                                                  0   100% /snap/compress-video/23  
>loop1  squashfs                                                  0   100% /snap/bare/5  
>loop2  squashfs                                                  0   100% /snap/core18/2344  
>loop3  squashfs                                                  0   100% /snap/core18/2409  
>loop4  squashfs                                                  0   100% /snap/core20/1518  
>loop5  squashfs                                                  0   100% /snap/gnome-3-28-1804/161  
>loop6  squashfs                                                  0   100% /snap/gtk-common-themes/1519  
>loop7  squashfs                                                  0   100% /snap/gtk-common-themes/1534  
>loop8  squashfs                                                  0   100% /snap/snapd/15904  
>loop9  squashfs                                                  0   100% /snap/snapd/16010  
>loop10 squashfs                                                  0   100% /snap/vlc/3008  
>sda  
>├─sda1 vfat           5C52-66F4                             480,8M     1% /boot/efi  
>├─sda2 ext4     mint  104b7e91-c286-429b-a3c2-3387acf555f6  
>├─sda3 vfat           5C2C-1816  
>├─sda5 swap           e3f23f0f-1bea-483f-96e0-7e130b58d6a9  
>└─**sda6 ext4           0d8fd723-9e58-4ec5-9345-3abe7557af80   30,7G    58% /**  
>sr0  

Podemos ver também que estamos usando 58% do espaço em disco disponível. Guarde essa informação porque ela será útil daqui a pouco!

O ext4 tem vários mecanismos para diminuir a fragmentação do sistema de arquivos, porém mesmo assim ele pode ficar fragmentado. 

Os sistemas ext2 e ext3 fragmentavam de forma considerável (em relação ao ext4) porque usavam alocação em bloco enquanto o ext4 usa alocação por extent(extensão) que  é um conjunto de blocos físicos contíguos, o que melhora o desempenho.

Mesmo assim ainda eram melhores que o NTFS da Microft, pois O NTFS guarda a informação nos blocos de uma forma contígua, o que faz com que vá surgindo pequenos fragmentos (tenta colocar os arquivos próximos um do outro),
ou seja, não tenta alocar os blocos de forma inteligente.

Já o Linux dispersa os ficheiros ao longo do disco, deixando uma quantidade de espaço considerável entre eles - o próprio sistema pode reduzir os espaços sem necessidade de recorrer a ferramentas de desfragmentação

Mesmo assim quando a unidade de armazenamento está quase cheia os novos dados gravados irão se fragmentar (acima de 85%), ou seja, quando a unidade ficar com 10% de espaço livre (ou menos) a fragmentação será suficiente para 
justificar uma desfragmentação.

Além disso, arquivos muitos grandes e que vivem enchendo e esvaziando a unidade de armazenamento, como applicances de máquinas virtuais, provavelmente sofrerão fragmentação considerável.

Ou seja, em ambos os casos, se você tem problemas com fragmentação no Linux, provavelmente precisa de um disco maior.

# CHECANDO O NÍVEL DE FRAGMENTAÇÃO DO DISCO

Para checar o nível de fragmentação usaremo o comando **e4defrag** que no Ubuntu faz parte do pacote **e2fsprogs**. Para instalar basta fazer:

> leandro@leandro:~$ sudo apt install e2fsprogs  

Então para saber o nível de fragmentação do sistema rodamos:

>leandro@leandro:~$ sudo e4defrag -c /  
>[sudo] senha para leandro:   
>e4defrag 1.45.5 (07-Jan-2020)  
><Fragmented files>                                                     now/best       size/ext  
> 1\. /var/log/wtmp                                                     94/1              4 KB  
> 2\. /home/leandro/.ssr/logs/log-2022-04-05_22.20.36.txt               14/1              4 KB  
> 3\. /var/log/unattended-upgrades/unattended-upgrades-dpkg.log         11/1              4 KB  
> 4\. /home/leandro/snap/vlc/common/.cache/mesa_shader_cache/index      21/1              4 KB  
> 5\. /home/leandro/.ssr/logs/log-2022-03-12_17.55.44.txt               11/1              4 KB  
>  
> Total/best extents				445812/436810  
> Average size per extent			116 KB  
> **Fragmentation score				1**  
> [0-30 no problem: 31-55 a little bit fragmented: 56- needs defrag]  
> This directory (/) does not need defragmentation.  
> Done.  


Importante mencionar que a opção -c faz o e4defrag verificar a fragmentação do sistema, mas não vai desfragmentar nada.

O primeiro campo a analisar é  resultado no campo **Fragmentation Score**. No nosso caso o valor é 1. De acordo com a seguinte tabela:

> 0 a 30      => sem problemas  
> 31 a 55     => pequena fragmentação  
> acima de 55 => sistema de ser desfragmentado  

Vemos que não temos problemas de fragmentação, pois somente resultado acima de 56 é necessário realizar desfragmentação. 

Mas ao mesmo tempo o relatório mostra uma lista de arquivos que parecem fragmentados. Há cinco arquivos ou diretórios e, na terceira coluna(now/best) uma contagem de fragmentos usados e de fragmentos ideais. 

Veja que há duas entradas que estão dentro do diretório /var/log e outras duas dentro da pasta de usuário(/home/leandro). Devemos fazer alguma coisa? 

A resposta é: não devemos, mas podemos!

É que o valor de Fragmentation Score nos informa um valor indicando que não há necessidade de desfragmentar nada, mas o comando e4defrag permite desfragmentar arquivos e diretórios individualmente. Para desfragmentar, basta 
rodar o comando somente com o nome do arquivo ou diretório (sem o parâmetro -c).

>leandro@leandro:~$ sudo e4defrag /var/log/wtmp  
>[sudo] senha para leandro:   
>e4defrag 1.45.5 (07-Jan-2020)  
>ext4 defragmentation for /var/log/wtmp  
>  
>[1/1]/var/log/wtmp:	100%	[ OK ]  
> Success:			[1/1]  


>leandro@leandro:~$ sudo e4defrag /var/log/unattended-upgrades/unattended-upgrades-dpkg.log  
>e4defrag 1.45.5 (07-Jan-2020)  
>ext4 defragmentation for /var/log/unattended-upgrades/unattended-upgrades-dpkg.log  
>  
>[1/1]/var/log/unattended-upgrades/unattended-upgrades-dpkg.log:	100%	[ OK ]  
> Success:			[1/1]  


Se eu quisesse desfragmentar o diretório raiz(/), poderia rodar

> sudo e4defrag /

Importante mencionar que o e4defrag não funciona na partição de SWAP e que se pode usar o e4defrag como usuário normal, mas nesse caso só vai verificar e corrigir arquivos pertencentes ao usuário.

E também que unidades SSD não devem ser desfragmentadas pois pode diminuir sua vida útil.

E, por último, há uma outra forma de desfragmentar o disco : copiar todos os arquivos para outro disco, apagar os arquivos originais e copiá-los de volta. O sistema vai alocar os arquivos de forma eficiente 
à medida em que os copia para o disco.


