---
title: A saga do Tablet - parte 03
date: 2022-08-03
tags:
  - "ubuntu"
  - "linux"
  - "Android"
  - "Tablet"
  - "Usb"
  - "adb"
  - "Heimdall"	
categories:
  - "Linux"
  - "Artigos"
---
Em meu último artigo eu mostrei as dificuldades iniciais com o comando adb e fastboot que não funcionaram conforme eu esperava.

As dificuldades foram úteis porque serviram de aprendizado e apontaram a necessidade de uma nova abordagem.

Agora eu vou abandonar os dois comandos e vou partir para o Heimdall.
<!--more-->
Eu não queria usar inicialmente o Heimdall porque eu entendia que era uma ferramenta específica para recuperar instalações de
sistemas Android, assim como **Odin**. Na verdade é uma ferramenta para dispositivos Samsung (como o meu!)

Em minhas pesquisas vi alguns procedimentos para instalação de um novo sistema operacional que passavam por uma imagem de 
recuperação personalizada como procedimento inicial para instalação, por isso resolvi tentar o procedimento de gravar uma
imagem usando o Heimdall.

Basicamente os dispositivos Android têm o sistema operacional normal, que carrega a partir do bootloader e um boot alternativo
que é chamado de modo "recovery" e serve para algumas atividade de manutenção. Se eu gravasse uma nova imagem de recovery
eu poderia seguir adiante.

O Heimdall faz parte do repositório do Ubuntu, então a instalação é bem direta:

> leandro@leandro:~$ sudo apt search heimdall-flash heimdall-flash-frontend

Sendo que eu instalei uma versão de linha de comando e uma versão gráfica. Eu pretendo usar a interface de linha de comando,
mas dependendo do resultado eu posso tentar a forma gráfica (que possui muito material de apoio na Internet).

A man page do Heimdall informa que é uma ferramenta para dispositivos Samsung Galaxy S, então pode ser que eu encontre
problemas com meu tablet antigo, mas isso não me desanima.

É bom lembrar também que eu não tenho acesso root ao dispositivo (eu não consigo executar o shell dentro do Android como
administrador).

Para executar o Heimdall, é necessário que o tablet esteja em modo Download. Apenas para recordar, os dispositivos Samsung
não possuem suporte ao modo fastboot, mas em seu lugar existe esse modo Download que é próprio para gravar imagens.

Para entrar no modo Download no dispositivo, é necessário apertar a combinação de botões **Power + Vol Down**.

Depois de muitos testes eu acabei achando na internet um modo de entrar nesse modo especial pela linha de comando:

> leandro@leandro:~$ adb reboot download

Esse comando não está documentado na man page do adb, por isso #fikadica!

Primeiro eu verifiquei se o dispositivo estava sendo detectado em modo Download pelo Heimdall:

> leandro@leandro:~$ heimdall detect  
> Device detected  

Então eu baixei a tabela de particionamento do dispositivo, que é chamado de pit:

>leandro@leandro:~$ heimdall print-pit  
>Heimdall v1.4.2  
>  
>Copyright (c) 2010-2017 Benjamin Dobell, Glass Echidna  
>http://www.glassechidna.com.au/  
>  
>This software is provided free of charge. Copying and redistribution is  
>encouraged.  
>  
>If you appreciate this software and you would like to support future  
>development please consider donating:  
>http://www.glassechidna.com.au/donate/  
>  
>Initialising connection...  
>Detecting device...  
>Claiming interface...  
>Attempt failed. Detaching driver...  
>Claiming interface again...  
>Setting up interface...  
>  
>Initialising protocol...  
>Protocol initialisation successful.  
>  
>Beginning session...  
>  
>Some devices may take up to 2 minutes to respond.  
>Please be patient!  
>  
>Session begun.  
>  
>Downloading device's PIT file...  
>PIT file download successful.  
>  
>Entry Count: 13  
>Unknown 1: 0  
>Unknown 2: 0  
>Unknown 3: 0  
>Unknown 4: 0  
>Unknown 5: 0  
>Unknown 6: 0  
>Unknown 7: 0  
>Unknown 8: 0  
>  
>  
>--- Entry #0 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 0  
>Attributes: 0 (Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 1  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: IBL+PBL  
>Flash Filename: boot.bin  
>FOTA Filename:   
>  
>  
>--- Entry #1 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 1  
>Attributes: 0 (Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 1  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: PIT  
>Flash Filename: p1wifi.pit  
>FOTA Filename:   
>  
>  
>--- Entry #2 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 20  
>Attributes: 2 (STL Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 40  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: EFS  
>Flash Filename: efs.rfs  
>FOTA Filename:   
>  
>  
>--- Entry #3 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 3  
>Attributes: 0 (Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 5  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: SBL  
>Flash Filename: sbl.bin  
>FOTA Filename:   
>  
>  
>--- Entry #4 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 4  
>Attributes: 0 (Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 5  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: SBL2  
>Flash Filename: sbl.bin  
>FOTA Filename:   
>  
>  
>--- Entry #5 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 21  
>Attributes: 2 (STL Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 20  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: PARAM  
>Flash Filename: param.lfs  
>FOTA Filename:   
>  
>  
>--- Entry #6 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 5  
>Attributes: 0 (Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 30  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: NORMALBOOT  
>Flash Filename: normalboot.img  
>FOTA Filename:   
>  
>  
>--- Entry #7 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 8  
>Attributes: 0 (Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 30  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: RECOVERY  
>Flash Filename: recovery.img  
>FOTA Filename:   
>  
>  
>--- Entry #8 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 22  
>Attributes: 2 (STL Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 1430  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: SYSTEM  
>Flash Filename: system.rfs  
>FOTA Filename:   
>  
>  
>--- Entry #9 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 23  
>Attributes: 2 (STL Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 302  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: USERDATA  
>Flash Filename: userdata.rfs  
>FOTA Filename:   
>  
>  
>--- Entry #10 ---  
>Binary Type: 0 (AP)  
>Device Type: 0 (OneNAND)  
>Identifier: 24  
>Attributes: 2 (STL Read-Only)  
>Update Attributes: 0  
>Partition Block Size/Offset: 256  
>Partition Block Count: 140  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: CACHE  
>Flash Filename: cache.rfs  
>FOTA Filename:   
>  
>  
>--- Entry #11 ---  
>Binary Type: 0 (AP)  
>Device Type: 2 (MMC)  
>Identifier: 3  
>Attributes: 1 (Read/Write)  
>Update Attributes: 0  
>Partition Block Size/Offset: 0  
>Partition Block Count: 0  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: HIDDEN  
>Flash Filename: hidden.rfs  
>FOTA Filename:   
>  
>  
>--- Entry #12 ---  
>Binary Type: 0 (AP)  
>Device Type: 2 (MMC)  
>Identifier: 0  
>Attributes: 1 (Read/Write)  
>Update Attributes: 0  
>Partition Block Size/Offset: 0  
>Partition Block Count: 0  
>File Offset (Obsolete): 0  
>File Size (Obsolete): 0  
>Partition Name: MOVINAND  
>Flash Filename: movinand.mst  
>FOTA Filename:   
>   
>Ending session...  
>Rebooting device...  
>Releasing device interface...  
>Re-attaching kernel driver...  
 
Acima temos muitas informações interessantes, em especial, há 13 entradas separadas por seções. Cada seção contém
dados de uma partição do sistema Android. Em especial no interessa os campos **Device Type**, **Attributes**,**Partition Name**,
**Flash Filename**. 

Junte as informações deste arquivo com aquelas que obtivemos na [parte 02](/artigos/a-saga-do-tablet-02/) deste artigo e iremos aos poucos obtendo uma boa 
visão do sistema.

Ainda assim eu tenho muitas dúvidas acerca do armazenamento interno em dispositivos móveis. Eu tenho dois tipos de armazenamento
informados pelo arquivo **pit**: MMC e OneNAND.

MMC é o cartão de memória e OneNAND é a memória interna. Ainda não está claro para mim se as partições precisam ou não 
estar em posições específicas na memória. O registrador EIP deve receber um valor inicial padrão, mas o processo no meu
modelo de tablet ainda não está totalmente claro para mim. Mais pequisas nesse assunto então!

Seguindo adiante, eu vou testar algumas dicas da Internet e gravar a partição de Recovery com um aplicativo específico para isso.

Eu vou usar o **TWRP** tendo em vista o procedimento recomendado pelo **PostMarketOS**.

Eu achava que o TWRP instalava apenas via loja de aplicativos, o que me causaria problemas tendo em vista a dificuldade em instalar
coisas neste tablet. 

Mas no [site](https://twrp.me/) do projeto eu vi que existe arquivos de imagem disponíveis e era o que eu precisava!

Além disso, havia uma versão do **TWRP** disponível (para o meu tablet)[https://twrp.me/], o que me animou ainda mais!

Então fiz o download dessa versão:

> leandro@leandro:~/Downloads/tablet$ wget https://dl.twrp.me/espressowifi/twrp-3.4.0-0-espressowifi.img

Entrei em modo download com a combinação Power+Volume Down (eu ainda não tinha encontrado a dica de entrar no modo donwload
com o comando **ADB**)

Tablet posto em modo Dowload, agora era necessário gravar o **TWRP** na partição de recovery(--RECOVERY) com o Heimdall:

>leandro@leandro:~/Downloads/tablet$ heimdall flash --RECOVERY twrp-3.4.0-0-espressowifi.img  --no-reboot  
>Heimdall v1.4.2  
>
>Copyright (c) 2010-2017 Benjamin Dobell, Glass Echidna  
>http://www.glassechidna.com.au/  
>  
>This software is provided free of charge. Copying and redistribution is  
>encouraged.  
>  
>If you appreciate this software and you would like to support future  
>development please consider donating:  
>http://www.glassechidna.com.au/donate/  
>  
>Initialising connection...  
>Detecting device...  
>Claiming interface...  
>Attempt failed. Detaching driver...  
>Claiming interface again...  
>Setting up interface...  
>  
>Initialising protocol...  
>Protocol initialisation successful.  
>  
>Beginning session...  
>  
>Some devices may take up to 2 minutes to respond.  
>Please be patient!  
>  
>Session begun.  
>  
>Downloading device's PIT file...  
>PIT file download successful.  
>  
>Uploading RECOVERY  
>100%  
>RECOVERY upload successful  
>  
>Ending session...  
>Releasing device interface...  
>Re-attaching kernel driver...  

A seguir reiniciei o tablet e ... nada! Tentei o procedimento novamente e entrei em modo recovery e... nada!

A mensagem acima indica que o procedimento havia obtido sucesso, mas por algum motivo algo estava saindo errado.

Eu li em alguns fóruns que após a gravação da partição era necessário entrar rapidamente em modo recovery do contrário o
Android sobrescreveria (recuperaria) o conteúdo original.

Eu tentei várias vezes e de diversas formas, tentando ser o mais rápido possível, até concluir que seria inútil.

Eu não sei dizer se havia algo de errado com meu ambiente ou com o tablet, se era por que eu não tinha acesso de root no
tablet ou não (apesar de que em modo download eu não precisaria do privilégio, eu acho). Enfim, tive de descartar essa
opção de solução.

Eu tinha agora como opção ir para um sistema Windows e tentar pelo software Odin, que é similar ao Heimdall. Talvez fosse
algum problema de arquitetura, quem sabe? 

O problema é que eu não tinha uma máquina Windows para fazer esse teste. 

Enquanto providenciava uma máquina eu me convenci de que seria inevitável recorrer a algum método para obter o acesso como
usuário root no aparelho. Ter acesso root facilitaria meu trabalho, até mesmo para obter mais informações!

Quando iniciei o périplo eu havia deixado de lado essa opção porque esses métodos fazem uso de exploits para contornar o
sistema operacional. Acho que eu tinha uma visão provinciana dos exploits...

Dado a dificuldade encontrada, era hora de apelar para qualquer coisa que me fornecesse o resultado que eu precisava.

[A saga do Tablet - parte 02](/artigos/a-saga-do-tablet-02/)

