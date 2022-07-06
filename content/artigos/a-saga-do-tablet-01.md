---
title: A saga do Tablet - parte 01
date: 2022-07-06
tags:
  - "ubuntu"
  - "linux"
  - "Android"
  - "Tablet"
  - "Usb"
categories:
  - "Linux"
---
Eu tenho um Galaxy Tab antigo, versão 7. Ele já não atualiza e a versão do Android e nem me
permite instalar nada pois a maior parte dos aplicativos é construído para uma versão mais nova
do sistema operacional.

Ele estava encostado aqui e, embora na teoria eu ainda possa utilizá-lo, na prática não posso!Nem mesmo a PlayStore
eu consigo acessar!

Navegar é possível em apenas alguns poucos sites e mesmo assim sou importunado por mensagens de 
alerta por problemas de certificados e ainda por sites toscamente renderizados.

Enfim, não perco nada se o danificar em uma empreitada...

<!--more-->
Eu pretendo instalar algo que me permita utilizá-lo. O mínimo de que preciso é de um
navegador funcional, que me permita acessar à maioria dos sites que eu gosto.

Então a tarefa é instalar uma distribuição Linux nele ou instalar uma nova ROM. Minha primeira tenta-tiva
será instalar uma distribuição Linux dentro dele. Se não conseguir, parto para a ROM.

A primeira coisa a fazer, então, é saber o que eu tenho em mãos para minimizar os riscos que irei correr.
O modelo é GT-P1010, então vasculhando pela internet, encontrei:

|                  |                                          |
| -------------: | --------------------------------------------- |
| Modelo:   | GT-P1010 Galaxy Tab 7.0                       |
| Processador:   | S5PC110 (OMAP 3621) **32 bits**                   |
| Tipo de Core:    |ARM Cortex-A8                                  |
| Instruções suportadas: | **ARM v7**                                        |
| Velocidade:   | 1 Ghz                                         |
| Núcleos:  | 01                                            |
| GPU: | **PowerVR SGX530**                                |
| Tamanho:   | 7''                                           |
| Resolução: |**1024 x 600 px 170 ppi** (~60.2% ratio corpo-tela)|
| Tipo de Display:  | Color TN-TFT LCD                              | 
| Tela:  | Capacitive multi-touch screen                 |
| Memória interna:   | **16 Gb**                                         |
| Memória RAM: | **512 Mb**                                        |
| Bateria:  | Li-Ion 4000 mAh                               |
| Cartão de memória:  | microSD, microSDHC (até 32 Gb)                |
| Câmera:  | 3.15 MPx                                      |
| Bluetooth:  | sim (A2DP\| EDR)                               |
| Wifi:  | 802.11b, 802.11g, 802.11n                     |
| GPS:  | sim                                           |


O relatório acima não é muito confiável porque tirei de vários sites da Internet, mas podemos ver 
que vamos precisar de uma distribuição Linux com os seguintes: 
requisitos:
* processador ARM
* 32 bits
* consuma menos de 256 Mb com interface gráfica

## Preparação

A primeira coisa a ser feita era conectar o tablet ao computador e de início tive meu primeiro problema:

> leandro@leandro:~$ adb devices  
> List of devices attached   
> ????????????	no permissions  

Pela mensagem o dispositivo havia sido reconhecido, mas não havia sido encontrado. Pesquisando um 
pouco pela internet, encontrei uma sugestão [aqui](http://ptspts.blogspot.com/2011/10/how-to-fix-adb-no-permissions-error-on.html)
que funcionou perfeitamente.
A solução seria criar um arquivo **51-android.rules** dentro de **/etc/udev/rules.d/** com o seguinte conteúdo:

```
SUBSYSTEM=="usb", ATTRS{idVendor}=="0bb4", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0e79", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0502", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0b05", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="413c", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0489", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="091e", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="18d1", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0bb4", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="12d1", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="24e3", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="2116", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0482", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="17ef", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="1004", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="22b8", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0409", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="2080", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0955", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="2257", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="10a9", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="1d4d", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0471", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="04da", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="05c6", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="1f53", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="04e8", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="04dd", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0fce", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0930", MODE="0666"
SUBSYSTEM=="usb", ATTRS{idVendor}=="19d2", MODE="0666"
```
Depois precisei desconectar o tablet e recarregar as regras do udev rodando o comando:

> sudo udevadm control -R

E então conectar o tablet novamente. Verificando o dispositivo vemos:

> leandro@leandro:~$ adb devices  
> List of devices attached   
> 10106419c016	device  

Contudo, fiquei curioso com a solução. Conectando o tablet e verificando pelo lsusb:

>leandro@leandro:~$ lsusb  
>**Bus 002 Device 006: ID 04e8:681c Samsung Electronics Co., Ltd Galaxy Portal/Spica/S**  
>Bus 002 Device 005: ID 1a2c:2c27 China Resource Semico Co., Ltd   
>Bus 002 Device 004: ID 1a40:0101 Terminus Technology Inc. Hub  
>Bus 002 Device 003: ID 1ea7:0064 SHARKOON Technologies GmbH   
>Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub  
>Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub  
>Bus 001 Device 003: ID 5986:053a Acer, Inc   
>Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub  
>Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub  


A saída do tablet está destacada na listagem do usb e podemos ver que o id de fabricante e de produto
é 04e8:681c, sendo **04e8** o ID de fabricante e **681c** o ID de produto. 

Será que encontraremos algum desses identificadores no arquivo /etc/udev/rules.d/51-android.rules?

>leandro@leandro:~$ grep 04e8  /etc/udev/rules.d/51-android.rules  
>SUBSYSTEM=="usb", ATTRS{idVendor}=="04e8", MODE="0666"  
>leandro@leandro:~$ grep 681c  /etc/udev/rules.d/51-android.rules  
>leandro@leandro:~$   

Podemos ver que existe uma regra associanda ao ID de fabricante (**04e8**).

Isso significa que todas as outras regras são desnecessárias e podem ser removidas sem problema! E
mais! Vamos analisar a regra e ver o que ela faz:

> SUBSYSTEM=="usb", ATTRS{idVendor}=="04e8", MODE="0666"

Primeiramente essa é uma regra do UDEV. Ele é o gerenciador dinâmico de dispositivos e
recebe eventos do kernel quando um dispositivo é adicionado,removido ou tem seu estado alterado no 
sistema.

Esse sub sistema do kernel é gerido por meio de regras e acima adicionamos uma.

Cada linha em um arquivo de regras e contém várias linhas onde cada uma contém ao menos um par chave-valor
(com exceção dos comentários). Há dois tipos de chaves: correspondência(match) e atribuição(assignment).

Resumindo, correspondências são verificações (como se fosse um **IF** em uma linguagem de programação)
enquanto a atribuição é justamente isso.

Além disso, cada par chave-valor possui uma operação, que pode ser:

|  |   |
| ---- | --- |
| **==** |comparação de igualdade |
| **!=** | comparação por diferença|
| **=** | atribuição de valor|
| **+=** |adiciona o valor a uma chave que contém uma lista de entradas|
| **-=** |remove o valor a uma chave que contém uma lista de entradas|
| **:=** |atribui um valor a uma chave e desabilita mudanças posteriores|

Então, voltando à nossa regra, seria como se tivéssemos:
```
IF SUBSYSTEM=="usb" AND ATTRS{idVendor}=="04e8" THEN 
  MODE="0666"
ENDIF
```
Da man page do udev temos que:

* **SUBSYSTEM**: Pesquise por um nome de subsistema de dispositivo correspondente. Corresponde a uma
busca por um nome de diretório dentro do sistema de arquivos /sys.

* **ATTRS**: Pesquise por um nome de atributo correspondente. Corresponde a uma busca por nome de 
arquivo dentro do sistema de arquivos /sys e dentro de SUBSYSTEM no nosso caso.

A regra acima procura por um diretório de nome usb dentro do sistema de arquivos /sys e, dentro desse
diretório, por um arquivo de nome idVendor com conteúdo 04e8. Será que ele existe?

O subsistema usb está definido em **/sys/bus/usb**
> leandro@leandro:/sys/bus/usb$ ls  
> **devices**  drivers  drivers_autoprobe  drivers_probe  uevent  

O que nos interessa está dentro do subdiretório **devices**. Dentro desse diretório temos vários
sub diretórios, correspondendo aos dispositivos usb encontrados pelo kernel e que podem ser listados
pelo comando **lsusb**. A opção -t do lsusb nos fornece uma saída mais fácil para localizar os 
dispositivos:

>leandro@leandro:~$ lsusb -t  
>/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=ehci-pci/2p, 480M  
>&nbsp;   |__ Port 1: Dev 2, If 0, Class=Hub, Driver=hub/4p, 480M  
>&nbsp;&nbsp;        |__ Port 1: Dev 3, If 0, Class=Human Interface Device, Driver=usbhid, 12M  
>&nbsp;&nbsp;        |__ Port 2: Dev 4, If 0, Class=Hub, Driver=hub/4p, 480M  
>&nbsp;&nbsp;&nbsp;            |__ Port 1: Dev 5, If 1, Class=Human Interface Device, Driver=usbhid, 1.5M  
>&nbsp;&nbsp;&nbsp;            |__ Port 1: Dev 5, If 0, Class=Human Interface Device, Driver=usbhid, 1.5M  
>&nbsp;&nbsp;&nbsp;            |__ Port 2: Dev 6, If 0, Class=Communications, Driver=cdc_acm, 480M  
>&nbsp;&nbsp;&nbsp;            |__ Port 2: Dev 6, If 1, Class=CDC Data, Driver=cdc_acm, 480M  
>&nbsp;&nbsp;&nbsp;            |__ Port 2: Dev 6, If 2, Class=Mass Storage, Driver=usb-storage, 480M  
>&nbsp;&nbsp;&nbsp;            |__ Port 2: Dev 6, If 3, Class=Vendor Specific Class, Driver=, 480M**  
>\/:  Bus 01.Port 1: Dev 1, Class=root_hub, Driver=ehci-pci/2p, 480M  
>&nbsp;&nbsp;    |__ Port 1: Dev 2, If 0, Class=Hub, Driver=hub/4p, 480M  
>&nbsp;&nbsp;&nbsp;        |__ Port 1: Dev 3, If 0, Class=Video, Driver=uvcvideo, 480M  
>&nbsp;&nbsp;&nbsp;        |__ Port 1: Dev 3, If 1, Class=Video, Driver=uvcvideo, 480M  

Seguindo o padrão bus-port para representar uma árvore, como pode ser visto abaixo. Compare com a 
saída do lsusb acima.

>leandro@leandro:/sys/bus/usb/devices$ ls -l
>total 0
>lrwxrwxrwx 1 root root 0 jul  5 21:04 1-0:1.0 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-0:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 1-1 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 1-1.1 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1.1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 1-1:1.0 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 1-1.1:1.0 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1.1/1-1.1:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 1-1.1:1.1 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1.1/1-1.1:1.1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-0:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-0:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.1 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.1:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.1/2-1.1:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.2 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.2.1 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.2:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.2.1:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.1/2-1.2.1:1.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 2-1.2.1:1.1 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.1/2-1.2.1:1.1  
>lrwxrwxrwx 1 root root 0 jul  5 21:34 2-1.2.2 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2  
>lrwxrwxrwx 1 root root 0 jul  5 21:34 2-1.2.2:3.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2/2-1.2.2:3.0  
>lrwxrwxrwx 1 root root 0 jul  5 21:34 2-1.2.2:3.1 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2/2-1.2.2:3.1  
>lrwxrwxrwx 1 root root 0 jul  5 21:34 2-1.2.2:3.2 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2/2-1.2.2:3.2  
>lrwxrwxrwx 1 root root 0 jul  5 21:34 2-1.2.2:3.3 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2/2-1.2.2:3.3  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 usb1 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1  
>lrwxrwxrwx 1 root root 0 jul  5 21:04 usb2 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2  

Meu tablet está está em **Bus 002 Device 006**(primeiro lsusb rodado no início). Localizando na estrutura
em árvore do lsusb anterior, o endereçamento fica: 2-1.2.2. Entrando, então, no subdiretório (/sys/bus/usb/devices/2-1.2.2
) podemos ver um arquivo de nome idVendor dentro dele. 

>leandro@leandro:/sys/bus/usb/devices/2-1.2.2$ ls  
>2-1.2.2:3.0  avoid_reset_quirk    bDeviceSubClass     bNumInterfaces  devnum     **idVendor**      power      rx_lanes   uevent  
>2-1.2.2:3.1  bcdDevice            bmAttributes        busnum          devpath    ltm_capable   product    serial     urbnum  
>2-1.2.2:3.2  bConfigurationValue  bMaxPacketSize0     configuration   driver     manufacturer  quirks     speed      version  
>2-1.2.2:3.3  bDeviceClass         bMaxPower           descriptors     ep_00      maxchild      removable  subsystem  
>authorized   bDeviceProtocol      bNumConfigurations  dev             idProduct  port          remove     tx_lanes  

Qual será o conteúdo de idVendor?
>leandro@leandro:/sys/bus/usb/devices/2-1.2.2$ cat idVendor   
>**04e8**  

Bingo! Será que tem um jeito mais fácil de descobrir como esse dispositivo é montado? Como criaríamos 
uma regra para ele se fosse necessário?

O UDEV tem uma ferramenta para gerenciamento chamada **udevadm** que nos permite monitorar os eventos
tanto do KERNEL quanto do próprio UDEV.

Portanto, vou desconectar o tablet. Iniciar o udevadm e conectar o tablet novamente:

>leandro@leandro:~$ **udevadm monitor -p -u**  
>monitor will print the received events for:  
>UDEV - the event which udev sends out after rule processing  
>\...  
>UDEV  [2684.320986] add      /devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2 (usb)  
>ACTION=add  
>**DEVPATH=/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2**  
>**SUBSYSTEM=usb**  
>**DEVNAME=/dev/bus/usb/002/008**  
>DEVTYPE=usb_device  
>PRODUCT=4e8/681c/400  
>TYPE=2/0/0  
>BUSNUM=002  
>DEVNUM=008  
>SEQNUM=3672  
>USEC_INITIALIZED=2684320510  
>ID_VENDOR=SAMSUNG  
>ID_VENDOR_ENC=SAMSUNG  
>**ID_VENDOR_ID=04e8**  
>ID_MODEL=SAMSUNG_Android  
>ID_MODEL_ENC=SAMSUNG_Android  
>ID_MODEL_ID=681c  
>ID_REVISION=0400  
>ID_SERIAL=SAMSUNG_SAMSUNG_Android_10106419c016  
>ID_SERIAL_SHORT=10106419c016  
>ID_BUS=usb  
>ID_USB_INTERFACES=:020201:0a0000:080650:ff4201:  
>ID_VENDOR_FROM_DATABASE=Samsung Electronics Co., Ltd  
>ID_MODEL_FROM_DATABASE=Galaxy Portal/Spica/S  
>ID_PATH=pci-0000:00:1d.0-usb-0:1.2.2  
>ID_PATH_TAG=pci-0000_00_1d_0-usb-0_1_2_2  
>**DRIVER=usb**  
>**MAJOR=189**  
>**MINOR=135**  
>...  

A saída é extensa e eu fiz apenas um recorte para o que nos interessa. Acima temos a execução de uma
regra de adição(add), ou seja, o dispositivo está sendo adicionado ao sistema e uma série de 
informações. 

O que nos interessa é
> DEVPATH=/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.2/2-1.2.2  

> DEVNAME=/dev/bus/usb/002/008  

A primeira variável informa onde está o arquivo idVendor que contém o valor do id do fabricante 
enquanto a segunda variável informa onde o dispositivo está sendo criado no sistema de arquivos /dev

De fato, procurando as permissões do arquivo de dispositivos, temos:
>leandro@leandro:~$ ls -l /dev/bus/usb/002/008  
>crw-rw-rw- 1 root root 189, 135 jul  5 21:49 /dev/bus/usb/002/008  

Ou seja **0666 (MODE="0666")**.Ufa!

Todo esse rodeio para explicar que a regra muda a permissão do arquivo no diretório /dev correspondendo
ao dispositivo adicionado à entrada usb para 666 (leitura e escrita para o usuário, para o grupo e para todos).

Como eu pretendo trabalhar nesse tablet com calma, vou deixar para analisar as distribuições candidatas
para instalação na parte 02 desta série de artigos (que aliás eu nem sei se conseguirei realmente instalar algo nele, mas já sei que tem de rodar um processador ARM v7
de 32 bits!). Foi um bom começo ein!



Fonte:   
**http://ptspts.blogspot.com/2011/10/how-to-fix-adb-no-permissions-error-on.html**  
**man udev**  
**man udevadm**  



