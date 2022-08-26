---
title: A saga do Tablet - parte 04
date: 2022-08-25
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
Continuando a empreitada de exploração do tablet, vou partir para obtenção de acesso root no dispositivo.

Antes, porém, vou testar uma versão do software Odin para Linux, ou melhor, multiplataforma.
<!--more-->
Como eu mencionei no último artigo, eu ia partir para os exploits para obter acesso root. Pesquisando um pouco, eu encontrei referência para uma aplicação java que seria igual ao Odin. Resolvi testá-la.

>leandro@leandro:~/Downloads$ **git clone https://github.com/GameTheory-/jodin3.git**  
>leandro@leandro:~/Downloads/jodin3$ **cd jodin3/**  
>leandro@leandro:~/Downloads/jodin3$ **ls**  
>app  JOdin3CASUAL  README.md  runtime  

Para rodar a aplicação basta executar o arquivo JOdin3CASUAL

> leandro@leandro:~/Downloads/jodin3$ **./JOdin3CASUAL**

Isso vai abrir a seguinte interface, bem familiar àqueles que já experimentaram o Odin em ambiente Windows. É possível abrir o programa apenas clicando com o botão direito do mouse sobre
o arquivo e depois em iniciar, mas iniciando pela linha de comando teremos algumas informações extra que serão de grande valia mais à frente.

![Tela 01](/img/post/art03/jodin01_m.png)

Neste caso eu devo fornecer um arquivo PIT e o arquivo com a imagem de recovery deve estar no formato tar. Eu baixei uma imagem do TWRP neste formato:

>leandro@leandro:~/Downloads$ wget https://dl.twrp.me/espresso3g/twrp-3.1.0-0-espresso3g.img.tar

Então eu vou baixar o arquivo a partir do meu tablet colocando-o em modo download pela linha de comando conforme dica minha publicada
no site [Viva o Linux](https://www.vivaolinux.com.br/dica/Colocar-dispositivo-movel-Samsung-em-modo-Download-pela-linha-de-comando/):
>leandro@leandro:~/.config/pulse$ **adb reboot download**

E a seguir salvando o arquivo .PIT na minha máquina:

>leandro@leandro:~/Downloads/tablet$ **heimdall download-pit --output tablet.pit**  
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
>Ending session...  
>Rebooting device...  
>Releasing device interface...  
>Re-attaching kernel driver...  


Clicando sobre os campos "PIT" e "PDA" e preenchendo-os adequadamente conforme a próxima tela. Temos:

![](/img/post/art03/jodin02_m.png)

Clicando em "Start", obtemos a seguinte tela, onde aparentemente tudo deu certo.

![](/img/post/art03/jodin03_m.png)

Aparentemente... mas só que não!

Na verdade não deu certo novamente e olhando o log do programa pelo terminal é possível saber o porquê:

>[VERBOSE]**Heimdall Device detected!**  
>[VERBOSE]running command  
>[DEBUG]**Run Heimdall from DeviceCommunicationProtocol:/usr/bin/heimdall flash --PIT /home/leandro/Downloads/tablet/tablet.pit --RECOVERY /tmp/CASUALleandro-2022-08-24-23.26.25/recovery.img**  
>,[DEBUG]###executing real-time command: /usr/bin/heimdall###  
>[DEBUG]Attempting state change to 0 devices connected  

O que ele fez foi apenas rodar o Heimdall!!!! Ou seja, esse JOdin é apenas um frontend alternativo para o Heimdall!!! Decepcionante!!!

Eu gostaria de comentar algumas coisas que havia esquecido no artigo anterior.

A primeira é que, durante minha pesquisa, várias fontes mencionaram a possibilidade de que o Heimdall não fosse capaz de gravar a imagem de recovery. Neste caso a recomendação era para
desmontar o tablet, desconectar a bateria e ligar novamente. 

Eu quero evitar chegar a esse procedimento, mas não o descarto. Vou tentar todas as alternativas antes.

Então comecei a estudar os exploits. O primeiro que testei não era exatamente para Samsung, mas os procedimentos me pareceram plausíveis então resolvi testar. Não
é uma boa ideia sair testando dicas aleatórias, mas como estou registrando tudo o que fiz, vale a pena como aprendizado.

Os passos estão descritos neste link [aqui](https://www.dropbox.com/sh/jcpilpgoeta516e/AADr5I-wkB7doiTcIVKK9CW1a?preview=Rooting-the-tf300.txt)

Coloquei uma versão do comando **debugfs** e **su** dentro do tablet em um diretório com permissão global de escrita:

>leandro@leandro:~/Downloads/tablet$ **adb push debugfs /data/local**  
>debugfs: 1 file pushed. 1.0 MB/s (1862336 bytes in 1.838s)  
>leandro@leandro:~/Downloads/tablet$ **adb push su /data/local**  
>su: 1 file pushed. 1.2 MB/s (372006 bytes in 0.304s)  

Verificando se os arquivos estão lá:

>leandro@leandro:~/Downloads/tablet$ **adb shell ls /data/local**  
>tmp  
>tspreq  
>tsprsp  
>debugfs  
>su  

O script tenta criar um link simbólico entre uma partição de sistema e o diretório /data/local que é de leitura/escrita para todos e espera que o Android se enrole com links simbólicos.

>leandro@leandro:~/Downloads/tablet$ **adb shell**   
>$ cd /data/local  
>$ mv tmp tmp.back  
>$ ln -s /dev/block/stl9 tmp  
>$ exit  

Resumi os procedimentos porque eles estão documentados no link acima. E testando...

>leandro@leandro:~/Downloads/tablet$ **adb shell**  
>$ /data/local/debugfs -w /dev/block/stl9  
>debugfs 1.42 (29-Nov-2011)  
>/dev/block/stl9: Permission denied while opening filesystem  
>debugfs:  q  
>$ /data/local/debugfs /dev/block/stl9  
>debugfs 1.42 (29-Nov-2011)  
>/dev/block/stl9: Permission denied while opening filesystem  
>debugfs:  q  
>$ exit  

Infelizmente não funcionou!

Seria muita sorte se funcionasse considerando a natureza aleatória da dica! 

Mas na verdade essa minha primeira tentativa foi precipitada. Eu encontrei esse exploit pesquisando por outras coisas e além dele eu também encontrei uma referência um livro específico 
sobre Hacking em dispositivos Android. 

Trata-se do livro Xda developers' Android Hacker's toolkit - the complete guide to rooting, ROMs and theming dos autores Jason Tyler e Will Verduzco
que são editores do portal xda developers.O livro pode ser comprado [aqui](https://www.amazon.com.br/XDA-Developers-Android-Hackers-Toolkit-ebook/dp/B0087GZ49Q) apesar de ser uma edição antiga (eu não sei se há edições recentes).

Foi através da dica desse livro que consegui o acesso root na segunda tentativa.

Eu tentei o script **psneuter**. Baixei a versão pré compilada, mas no github é possível ver o código fonte e entender seu funcionamento, ou mesmo compilar por si próprio se não
confiar na versão binária disponibilizada.

>leandro@leandro:~/Downloads/tablet$ **wget https://github.com/tmzt/g2root-kmod/raw/master/scotty2/psneuter/psneuter**

Daí mandei para a pasta de permissão geral de leitura e escrita no tablet (a mesma usada na primeira tentativa):

> leandro@leandro:/tmp$ **adb push psneuter /data/local**  
> psneuter: 1 file pushed. 0.8 MB/s (557962 bytes in 0.670s)  

E rodei o arquivo...

>leandro@leandro:~/Downloads$ **adb shell**  
>$ /data/local/psneuter  
>property service neutered.  
>killing adbd. (should restart in a second or two)  

Será que funcionou????

>leandro@leandro:~/Downloads$ adb shell  
>\# id  
>**uid=0(root) gid=0(root)**  

Bingo! Root!

É bom esclarecer que eu testei o script psneuter por algumas peculiaridades: No Github eu vi que era de 12 anos atrás, ou seja, era mais ou menos da época de lançamento do meu tablet.

Se o script fosse muito posterior eu teria pouca confiança nele assim como se fosse anterior ao modelo de tablet. 

Outro ponto positivo foi a disponibilização de uma versão já compilada. Estou fazendo testes com binários compilados para arm e está difícil fazer funcionar. O problema é que o tablet é 
muito antigo e, quando eu compilo um programa para rodar nele, acontece um problema por causa da glibc ligada estaticamente ao executável. Há uma "trava" para bloquear a execução em
versões mais antigas do sistema operacional e isso está me causando problemas.

Isso não deve causar dor de cabeça em aparelhos e sistemas Android mais novos, mas no tablet está me causando muita irritação.

Bem, acesso root adquirido, é hora de tentar fazer algo produtivo com ele, mas vou deixar para o próximo artigo. Quero ainda me aprofundar no funcionamento do sistema Android.

Eu preciso entender o funcionamento especificamente no aparelho Samsung como o que eu tenho. É que a Internet está cheia de ruído e o excesso de dicas (e de dicas inúteis!!!) é um
problema.

Mas para não encerrar prematuramente, eis algumas informações que eu aproveitei para retirar:

>leandro@leandro:~$ **adb shell getprop**  
>[ro.secure]: [1]  
>[ro.allow.mock.location]: [0]  
>[ro.debuggable]: [0]  
>[persist.service.adb.enable]: [1]  
>[ro.factorytest]: [0]  
>[ro.serialno]: [c1609eb06dc432e]  
>[ro.bootmode]: [reboot_normal]  
>[ro.baseband]: [unknown]  
>[ro.carrier]: [unknown]  
>[ro.bootloader]: [unknown]  
>[ro.hardware]: [p1lite]  
>[ro.revision]: [4]  
>[dpm.allowcamera]: [1]  
>[ro.build.id]: [FROYO]  
>[ro.build.display.id]: [FROYO.UJKM5]  
>[ro.build.version.incremental]: [UJKM5]  
>[ro.build.version.sdk]: [8]  
>[ro.build.version.codename]: [REL]  
>[ro.build.version.release]: [2.2.2]  
>[ro.build.date]: [Mon Jun 13 08:39:01 BRT 2011]  
>[ro.build.date.utc]: [1307965141]  
>[ro.build.type]: [user]  
>[ro.build.user]: [sidclei]  
>[ro.build.host]: [bldhp-4]  
>[ro.build.tags]: [release-keys]  
>[ro.product.model]: [GT-P1010]  
>[ro.product.brand]: [samsung]  
>[ro.product.name]: [GT-P1010]  
>[ro.product.device]: [GT-P1010]  
>[ro.product.board]: [GT-P1010]  
>[ro.product.cpu.abi]: [armeabi-v7a]  
>[ro.product.cpu.abi2]: [armeabi]  
>[ro.product.manufacturer]: [samsung]  
>[ro.product.locale.language]: [en]  
>[ro.product.locale.region]: [GB]  
>[ro.wifi.channels]: []  
>[ro.board.platform]: [omap3]  
>[ro.build.product]: [GT-P1010]  
>[ro.build.description]: [GT-P1010-user 2.2.2 FROYO UJKM5 release-keys]  
>[ro.build.fingerprint]: [samsung/GT-P1010/GT-P1010/GT-P1010:2.2.2/FROYO/UJKM5:user/release-keys]  
>[ro.build.PDA]: [P1010UJKM5]  
>[ro.build.hidden_ver]: [P1010UJKM5]  
>[ro.build.changelist]: [1023452]  
>[ro.build.buildtag]: []  
>[ro.sf.lcd_density]: [240]  
>[ro.sf.hwrotation]: [90]  
>[rild.libpath]: [/system/lib/libsec-ril-apalone.so]  
>[rild.libargs]: [-d /dev/ttyS0]  
>[wifi.interface]: [tiwlan0]  
>[jpeg.libskiahw.decoder.enable]: [0]  
>[jpeg.libskiahw.decoder.thresh]: [100000]  
>[ro.opengles.version]: [131072]  
>[dalvik.vm.heapsize]: [48m]  
>[keyinputqueue.use_finger_id]: [true]  
>[windowsmgr.max_events_per_sec]: [60]  
>[ro.url.legal]: [http://www.google.com/intl/%s/mobile/android/basic/phone-legal.html]  
>[ro.url.legal.android_privacy]: [http://www.google.com/intl/%s/mobile/android/basic/privacy.html]  
>[ro.com.google.locationfeatures]: [1]  
>[ro.setupwizard.mode]: [DISABLED]  
>[ro.com.google.gmsversion]: [2.2_r10]  
>[media.stagefright.enable-player]: [false]  
>[media.stagefright.enable-meta]: [false]  
>[media.stagefright.enable-scan]: [false]  
>[media.stagefright.enable-http]: [false]  
>[media.stagefright.enable-record]: [false]  
>[keyguard.no_require_sim]: [true]  
>[ro.config.ringtone]: [Minimal_tone.ogg]  
>[ro.config.notification_sound]: [01_Sherbet.ogg]  
>[ro.config.alarm_alert]: [Good_Morning.ogg]  
>[ro.config.media_sound]: [Media_preview_Touch_the_light.ogg]  
>[net.bt.name]: [Android]  
>[net.change]: [net.wifi.http-proxy]  
>[dalvik.vm.stack-trace-file]: [/data/anr/traces.txt]  
>[ro.com.google.clientidbase]: [android-samsung]  
>[persist.sys.country]: [BR]  
>[persist.sys.localevar]: []  
>[persist.sys.language]: [pt]  
>[ro.FOREGROUND_APP_ADJ]: [0]  
>[ro.VISIBLE_APP_ADJ]: [1]  
>[ro.SECONDARY_SERVER_ADJ]: [2]  
>[ro.BACKUP_APP_ADJ]: [2]  
>[ro.HOME_APP_ADJ]: [4]  
>[ro.HIDDEN_APP_MIN_ADJ]: [7]  
>[ro.CONTENT_PROVIDER_ADJ]: [14]  
>[ro.EMPTY_APP_ADJ]: [15]  
>[ro.FOREGROUND_APP_MEM]: [1536]  
>[ro.VISIBLE_APP_MEM]: [2048]  
>[ro.SECONDARY_SERVER_MEM]: [4096]  
>[ro.BACKUP_APP_MEM]: [4096]  
>[ro.HOME_APP_MEM]: [4096]  
>[ro.HIDDEN_APP_MEM]: [5120]  
>[ro.CONTENT_PROVIDER_MEM]: [5632]   
>[ro.EMPTY_APP_MEM]: [6144]  
>[net.tcp.buffersize.default]: [4096,87380,110208,4096,16384,110208]  
>[net.tcp.buffersize.wifi]: [4095,87380,110208,4096,16384,110208]  
>[net.tcp.buffersize.umts]: [4094,87380,110208,4096,16384,110208]  
>[net.tcp.buffersize.edge]: [4093,26280,35040,4096,16384,35040]   
>[net.tcp.buffersize.gprs]: [4092,8760,11680,4096,8760,11680]  
>[init.svc.immvibed]: [stopped]  
>[init.svc.pvrsrvinit]: [stopped]  
>[init.svc.baseimage]: [stopped]  
>[init.svc.console]: [running]  
>[init.svc.servicemanager]: [running]   
>[init.svc.vold]: [running]  
>[init.svc.notified_event]: [running]  
>[init.svc.netd]: [running]  
>[init.svc.debuggerd]: [running]  
>[init.svc.ril-daemon]: [running]  
>[init.svc.DR-deamon]: [running]  
>[init.svc.mobex-daemon]: [running]  
>[init.svc.zygote]: [running]  
>[init.svc.media]: [running]  
>[init.svc.playlogo]: [stopped]  
>[init.svc.dbus]: [running]  
>[init.svc.installd]: [running]  
>[init.svc.keystore]: [running]  
>[init.svc.orientationd]: [running]  
>[init.svc.geomagneticd]: [running]  
>[init.svc.glgps]: [running]  
>[init.svc.adbd]: [running]  
>[ril.bt_macaddr]: [9463D1F01235]  
>[ril.wifi_macaddr]: [94:63:D1:F0:12:36]  
>[ril.serialnumber]: [RZ1B602331D]  
>[ro.csc.country_code]: [Brazil]  
>[ro.csc.sales_code]: [ZTO]  
>[init.svc.playsound]: [stopped]  
>[debug.sf.nobootanimation]: [0]  
>[ril.RildInit]: [1]  
>[init.svc.samsungloop]: [stopped]  
>[hw.keyboards.65537.devname]: [ear_key_driver]  
>[hw.keyboards.65538.devname]: [p1_keyboard]  
>[hw.keyboards.65539.devname]: [power_key_driver]  
>[hw.keyboards.65540.devname]: [touchscreen]  
>[sys.settings_system_version]: [12]  
>[net.hostname]: [android_a511c9ff800e1b49]  
>[EXTERNAL_STORAGE_STATE]: [mounted]  
>[EXTERNAL_STORAGE_STATE_SD]: [unmounted]  
>[init.svc.wlan_loader]: [stopped]  
>[dev.bootcomplete]: [1]  
>[sys.binder_expand_vma]: [0]  
>[wlan.driver.status]: [ok]  
>[init.svc.wpa_supplicant]: [running]  
>[net.dnschange]: [2]  
>[gsm.version.ril-impl]: [Samsung RIL(IPC) v2.0]  
>[dhcp.tiwlan0.result]: [ok]  
>[init.svc.dhcpcd]: [running]  
>[dhcp.tiwlan0.pid]: [1510]  
>[gsm.sim.operator.numeric]: []  
>[dhcp.tiwlan0.reason]: [BOUND]  
>[gsm.sim.operator.alpha]: []  
>[gsm.sim.operator.iso-country]: [BR]  
>[gsm.sim.state]: [UNKNOWN]  
>[gsm.current.phone-type]: [0]  
>[gsm.operator.alpha]: []  
>[gsm.operator.numeric]: []  
>[gsm.operator.iso-country]: []  
>[gsm.operator.isroaming]: [false]  
>[ril.ecclist0]: [911]  
>[dhcp.tiwlan0.dns1]: [192.168.15.1]  
>[dhcp.tiwlan0.dns2]: []  
>[dhcp.tiwlan0.dns3]: []  
>[dhcp.tiwlan0.dns4]: []  
>[dhcp.tiwlan0.ipaddress]: [192.168.15.4]  
>[dhcp.tiwlan0.gateway]: [192.168.15.1]  
>[dhcp.tiwlan0.mask]: [255.255.255.0]  
>[dhcp.tiwlan0.leasetime]: [43200]  
>[dhcp.tiwlan0.server]: [192.168.15.1]  
>[net.dns1]: [192.168.15.1]  
>[ro.runtime.firstboot]: [947247612298]  
>[ril.approved_codever]: [none]  
>[ril.approved_cscver]: [none]  
>[ril.official_cscver]: [P1010ZTOKF3]  
>[ril.encrypt_cscver]: [691A21C89C1278DB58A1AC020CF3C60B]  
>[net.wifi.http-proxy]: []  
>[sys.settings_secure_version]: [11]  
>[gsm.defaultpdpcontext.active]: [false]  

Como podemos ver, são informações completas sobre o sistema, inclusive do usuário que fez a compilação!!! 

É possível obter uma listagem mais resumida apenas do que tem de hardware no tablet:

>leandro@leandro:~$ **adb shell pm list features**  
>feature:reqGlEsVersion=0x20000  
>feature:android.hardware.bluetooth  
>feature:android.hardware.camera  
>feature:android.hardware.camera.autofocus  
>feature:android.hardware.camera.flash  
>feature:android.hardware.location  
>feature:android.hardware.location.gps  
>feature:android.hardware.location.network  
>feature:android.hardware.microphone  
>feature:android.hardware.sensor.accelerometer  
>feature:android.hardware.sensor.compass  
>feature:android.hardware.sensor.light  
>feature:android.hardware.touchscreen  
>feature:android.hardware.touchscreen.multitouch  
>feature:android.hardware.wifi  
>feature:android.software.live_wallpaper  
>feature:android.software.status_bar.quick_panel.brightness_settings  
>feature:android.software.status_bar.quick_panel.mini_controllers  
>feature:android.software.status_bar.quick_panel.quick_settings  

Eu quero pesquisar mais um pouco porque vou ver a possibilidade de modificar as opções de montagem dos sistemas de arquivos no fstab. Será que se eu modificar **ro** para **rw** eu
obtenho permissão de leitura/escrita? Será tão simples assim? E as partições extraídas no arquivo .PIT, onde se encaixam? Onde está o 
bootloader e como eu o acesso?

É claro que meu objetivo ainda é instalar um sistema Linux nele. Algumas coisas ainda não estão claras para mim. Entre elas se o esquema de particionamento é obrigatório ou não.

Eu vi que pelo menos inicialmente eu vou precisar aproveitar o particionamento para dar início ao instalador da distro. Não sei se consigo fazer um **dd** no tablet (conseguir eu 
consigo com root, mas não sei se vai funcionar). 

Ou seja, ainda há bastante coisa para entender antes de mudar radicalmente o sistema e que eu espero aprender.

## Conclusão

Ainda não foi dessa vez que comecei propriamente a reinstalação do sistema, mas consegui alguns avanços interessantes.

Testei uma suposta versão do Odin multiplataforma, que se mostrou um engodo completo e dois exploits. Um aleatório e outro específico.

O exploit específico eu vi no livro dos fundadores do portal xda developers e foi de grande ajuda. O livro também é muito bom!

Com acesso root, é hora de modificar o sistema. Apesar de que eu não esteja esperando facilidade.

Isso porque o procedimento do Heimdall deveria ter sido efetivo e não foi. Eu já estou considerando a necessidade de desmontar o tablet
e desconectar a bateria. 

Em todo caso ainda tenho coisas a explorar.

[Parte 03](/artigos/a-saga-do-tablet-03/)

[Parte 02](/artigos/a-saga-do-tablet-02/)

[Parte 01](/artigos/a-saga-do-tablet-01/)









