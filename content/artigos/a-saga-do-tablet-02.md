---
title: A saga do Tablet - parte 02
date: 2022-07-17
tags:
  - "ubuntu"
  - "linux"
  - "Android"
  - "Tablet"
  - "Usb"
  - adb
categories:
  - "Linux"
---
No artigo anterior eu encontrei algumas informações sobre meu tablet Samsung GT-P1010 e configurei
meu ambiente de modo a acessá-lo pelo terminal de comandos do Linux.

Agora eu tentarei instalar um sistema dentro dele. Vou tentar fazer apenas usando ferramentas de
linha de comando pelo terminal. Será que vai funcionar?
<!--more-->
Apenas para recordar, eu não tenho experiência alguma em dispositivos móveis além daquela de nível
usuário comum. Daí o título "Saga". 

Fazer essa instalação apenas pela linha de comando parece inocência, mas pela pesquisa que eu fiz
para iniciar essa empreitada eu vi que seria possível em dispositivos mais recentes. Por isso vou 
tentar estas técnicas primeiro. Se não funcionar será experiência adquirida.

Então primeiro vou verificar se o dispositivo está conectado:

leandro@leandro:~$ adb devices
List of devices attached
10106419c016	device

Fica então a decisão de qual sistema tentar instalar. Eu fiz uma boa pesquisa e descobri que há
muitas opções disponíveis. É bom lembrar que estou descartando a instalação por **CHROOT** ou qualquer
tipo de acesso via **VNC**. Eu quero linux nativamente no tablet!

Havia distribuições baseadas em Debian, Arch, Gentoo entre outras, que aparentemente permitiam a
instalação (preenchiam os requisitos técnicos) além de outras distribuições derivadas criadas 
especificamente para a plataforma.

Eu me decidi pelo [PostmarketOS](https://postmarketos.org/) por alguns motivos muito evidentes: havia extensa documentação para o
meu dispositivo e isso, para quem está começando, não pode ser negligenciado. Ela é baseada em Alpine
Linux que é uma distribuição que nunca usei e que tem algumas diferenças importantes em relação ao
Debian, mas que eu pretendia instalar para testar em uma máquina virtual.

O requisito de economia de recursos contou bastante. Se a instalação for bem sucedida, depois eu posso
experimentar outros sistemas para treinar meu conhecimento.

Na página wiki do [postmarketOS](https://wiki.postmarketos.org/wiki/Devices) esse tablet GT-P1010 que 
eu tenho está listado e já foi trabalhado por outras pessoas, assim posso esperar que praticamente tudo funcione (em teoria)!

Seguindo para a [página relacionada ao dispositivo](https://wiki.postmarketos.org/wiki/Samsung_Galaxy_Tab_2_7.0%22_(samsung-espresso3g))
eu tenho o procedimento a ser executado:
* baixar uma imagem pré-construída
* Instalar TWRP no tablet
* colocar o tablet em modo flash

Eu baixei uma imagem do postmarketOS baseada em XFCE4 para meu celular neste [link](https://images.postmarketos.org/bpo/v22.06/samsung-espresso3g/xfce4/).
Há dois arquivos para baixar (no momento em que estou escrevendo este artigo):

* 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img.xz
* 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g.img.xz

Eu vou realizar o download pelo wget pela linha de comando para documentar melhor as ações:

>leandro@leandro:~/Downloads/tablet$ wget https://images.postmarketos.org/bpo/v22.06/samsung-espresso3g/xfce4/20220713-0808/20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img.xz  
>  
>leandro@leandro:~/Downloads/tablet$ wget https://images.postmarketos.org/bpo/v22.06/samsung-espresso3g/xfce4/20220713-0808/20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g.img.xz  

Em seguida eu vou conferir a integridade dos arquivos. Afinal, não quero de forma alguma ser surpreendido
por algum problema desconhecido que eu não possa rastrear a origem. Por isso eu vou baixar o hash dos 
arquivos para comparar com o download que fiz

>leandro@leandro:~/Downloads/tablet$ wget https://images.postmarketos.org/bpo/v22.06/samsung-espresso3g/xfce4/20220713-0808/20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img.xz.sha256  
>  
>leandro@leandro:~/Downloads/tablet$ wget https://images.postmarketos.org/bpo/v22.06/samsung-espresso3g/xfce4/20220713-0808/20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g.img.xz.sha256  

Por fim eu vou checar a integridade dos dois arquivos de imagem usando o comando **sha256sum**
>leandro@leandro:~/Downloads/tablet$ sha256sum -c 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img.xz.sha256   
>20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img.xz: SUCESSO  
>  
>leandro@leandro:~/Downloads/tablet$ sha256sum -c 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g.img.xz.sha256   
>20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g.img.xz: SUCESSO  

Os dois arquivos estão íntegros, então é possível continuar. Eu vou descompactar os arquivos e obter
os arquivos img usando o comando **unxz**:

>leandro@leandro:~/Downloads/tablet$ unxz 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g.img.xz   
>  
>leandro@leandro:~/Downloads/tablet$ unxz 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img.xz  

Assim teremos dois arquivos .img que irão para o tablet em minha pasta de trabalho. 

Eu não tenho root neste aparelho e pelo que sei ele está bloqueado, mesmo assim vou tentar rodar 
a imagem de boot nele pelo comando **adb**:

> leandro@leandro:~/Downloads/tablet$ adb reboot 20220713-0808-postmarketOS-v22.06-xfce4-0.5.1-samsung-espresso3g-boot.img

O resultado foi um boot normal. Mas não custava nada tentar. Será necessário entrar no modo **fastboot**

O modo **fastboot** é uma protocolo usado para modificar o sistema de arquivos via conexão usb em um
computador desktop. Ele é usado via uma ferramenta de linha de comando com o mesmo nome.

Há uma variedade de tutoriais na internet e, por isso, alvo de muita confusão. Eu havia entendido que
era um método universal, mas a princípio era contraditório se eu precisava ou não entrar em um modo
especial do tablet para isso. Primeiro eu tentei pela linha de comando:

>leandro@leandro:~$ fastboot devices  
>leandro@leandro:~$  

Como não obtive saída, suspeitei de que seria necessário entrar em modo especial do celular.

Há uma combinação de teclas para fazer isso, mas vou tentar pela linha de comando com o **adb** para
ver se é possível. Tentei vários comandos:

> leandro@leandro:~/Downloads/tablet$ adb reboot-bootloader  
>  
> leandro@leandro:~/Downloads/tablet$ adb reboot bootloader  

Em todos o resultado foi o mesmo: Boot normal no tablet.
Tentei então entrar no modo recovery via **adb**:

>leandro@leandro:~/Downloads/tablet$ adb reboot recovery

Eu até consigo entrar em modo recuperação, mas

> leandro@leandro:~/Downloads/tablet$ fastboot devices

não me retorna nada. 

Tive de pesquisar pela Internet, novamente em fontes contraditórias tentando filtrar o que se aplicava
e o que não se aplicava ao meu tablet.

Na página sobre Android da [wiki do Arch Linux](https://wiki.archlinux.org/title/android#Fastboot) eu
obtive a informação de que dispositivos Samsung(como o meu!) não podiam ser gravados via Fastboot. Eu
havia encontrado esse link logo no começo de minha pesquisa, mas deixei meio de lado porque não sabia
se estava ou não atualizado. Pelo visto estava e a informação era confiável.

Pela Internet, verifiquei que além do modo Recovery, meu tablet também tinha o **modo Download** que
seria o método adequado para realizar o que eu queria. Mas será que esse modo Download não seria um
outro nome para o **Fastboot mode**? Eu precisava testar!

Entrei, então, em modo "Download" usando a combinação de teclas Power+Down+Home mas também nada feito!

> leandro@leandro:~/Downloads/tablet$ fastboot devices

Ao que parece é hora de reconhecer minha primeira derrota. Eu contava com a efetividade do comando
fastboot para fazer todo o trabalho para mim.

Apesar de o blog não possuir área para interação com o leitor, já posso escutar as risadas e também
os conselhos. Mas o objetivo não é apenas instalar uma ROM em um equipamento. É também fazer da forma
como eu quero e aprender alguma coisa no processo.

Eu submestimei meu equipamento! Bem, minha primeira tentativa foi esta. Na próxima será hora de tentar
o Heimdall, ainda insistindo na linha de comando!

Então, para não deixar o artigo desinteressante, vamos obter algumas informações sobre o sistema.

O comando **adb** permite executar comandos dentro do Android usando o comando **shell**:

Qual versão do kernel Linux o Android está usando?

>leandro@leandro:~/Downloads/tablet$ **adb shell cat /proc/version**  
>Linux version 2.6.32.9 (sidclei@bldhp-4) (gcc version 4.4.0 (GCC) ) #1 PREEMPT Mon Jun 13 08:27:20 BRT 2011  

Qual o processador da máquina?

>leandro@leandro:~/Downloads/tablet$ **adb shell cat /proc/cpuinfo**  
>Processor	: ARMv7 Processor rev 2 (v7l)  
>BogoMIPS	: 299.11  
>Features	: swp half thumb fastmult vfp edsp neon vfpv3   
>CPU implementer	: 0x41  
>CPU architecture: 7  
>CPU variant	: 0x3  
>CPU part	: 0xc08  
>CPU revision	: 2  
>  
>Hardware	: P1Lite Samsung Board  
>Revision	: 0004  
>Serial		: 0000000000000000  

Qual o driver de áudio?

>leandro@leandro:~/Downloads/tablet$ **adb shell cat /proc/asound/version**  
>Advanced Linux Sound Architecture Driver Version 1.0.21.  

Qual a placa de som?

>leandro@leandro:~/Downloads/tablet$ adb shell cat /proc/asound/cards  
> 0 [SDP3430        ]: twl4030 - SDP3430  
>                      SDP3430 (twl4030)  

Quais as interfaces de rede?

>leandro@leandro:~/Downloads/tablet$ **adb shell ls /proc/sys/net/ipv4/conf**  
>all  
>default  
>lo  
>usb0  
>sit0  
>ip6tnl0  
>tiwlan0  


Agora informações mais importantes. Quais as partições?

> leandro@leandro:~/Downloads/tablet$ adb shell cat /proc/partitions  
>major minor  #blocks  name  
>  
> 179        0   15630336 mmcblk0  
> 179        1   13359136 mmcblk0p1  
> 179        2    1966080 mmcblk0p2  
> 179        3     305088 mmcblk0p3  
> 139        0     513024 tfsr0/c  
> 139        1        256 tfsr1  
> 139        2        256 tfsr2  
> 139        3      10240 tfsr3  
> 139        4       1280 tfsr4  
> 139        5       1280 tfsr5  
> 139        6       5120 tfsr6  
> 139        7       7680 tfsr7  
> 139        8       7680 tfsr8  
> 139        9     366080 tfsr9  
> 139       10      77312 tfsr10  
> 139       11      35840 tfsr11  
> 137        0     513024 bml0/c  
> 137        1        256 bml1  
> 137        2        256 bml2  
> 137        3      10240 bml3  
> 137        4       1280 bml4  
> 137        5       1280 bml5  
> 137        6       5120 bml6  
> 137        7       7680 bml7  
> 137        8       7680 bml8  
> 137        9     366080 bml9  
> 137       10      77312 bml10  
> 137       11      35840 bml11  
> 138        3       6400 stl3  
> 138        6       1280 stl6  
> 138        9     355840 stl9  
> 138       10      72704 stl10  
> 138       11      32000 stl11  

Podemos notar a existência de partições com nomes estranhos. Isso porque o Android é baseado no kernel
Linux mas não segue o padrão de nomes dos diretórios (aliás ele cria os dele próprio!).

E como elas estão montadas?

>leandro@leandro:~/Downloads/tablet$ **adb shell mount**  
>rootfs / rootfs ro,relatime 0 0  
>tmpfs /dev tmpfs rw,relatime,mode=755 0 0  
>devpts /dev/pts devpts rw,relatime,mode=600 0 0  
>proc /proc proc rw,relatime 0 0  
>sysfs /sys sysfs rw,relatime 0 0  
>none /acct cgroup rw,relatime,cpuacct 0 0  
>tmpfs /mnt/asec tmpfs rw,relatime,mode=755,gid=1000 0 0  
>tmpfs /app-cache tmpfs rw,relatime,size=12288k 0 0  
>none /dev/cpuctl cgroup rw,relatime,cpu 0 0  
>/dev/block/stl9 /system rfs ro,relatime,vfat,log_off,check=no,gid/uid/rwx,iocharset=utf8 0 0  
>/dev/block/mmcblk0p3 /mnt/apk vfat ro,nodev,noatime,nodiratime,fmask=0133,dmask=0022,codepage=cp437,iocharset=iso8859-1,shortname=mixed,utf8,errors=remount-ro 0 0  
>/dev/block/mmcblk0p2 /data rfs rw,nosuid,nodev,relatime,vfat,llw,check=no,gid/uid/rwx,iocharset=utf8 0 0  
>/dev/block/stl10 /dbdata rfs rw,nosuid,nodev,relatime,vfat,llw,check=no,gid/uid/rwx,iocharset=utf8 0 0  
>/dev/block/stl11 /cache rfs rw,nosuid,nodev,relatime,vfat,llw,check=no,gid/uid/rwx,iocharset=utf8 0 0  
>/dev/block/stl3 /efs rfs rw,nosuid,nodev,relatime,vfat,llw,check=no,gid/uid/rwx,iocharset=cp437 0 0  
>/dev/block/stl6 /mnt/.lfs j4fs rw,relatime 0 0  
>debugfs /debug debugfs rw,relatime 0 0  
>/dev/block/vold/179:1 /mnt/sdcard vfat rw,dirsync,nosuid,nodev,noexec,noatime,nodiratime,uid=1000,gid=1015,fmask=0002,dmask=0002,allow_utime=0020,codepage=cp437,iocharset=iso8859-1,shortname=mixed,utf8,errors=remount-ro 0 0  

Note as partições que são montadas como somente leitura(**ro**) e as que são montadas com permissão de escrita(**rw**).

Além disso, como elas estão usando o espaço de armazenamento?

> leandro@leandro:~/Downloads/tablet$ **adb shell df**  
> /dev: 244676K total, 0K used, 244676K available (block size 4096)  
> /mnt/asec: 244676K total, 0K used, 244676K available (block size 4096)  
> /app-cache: 12288K total, 0K used, 12288K available (block size 4096)  
> /system: 353984K total, 342484K used, 11500K available (block size 4096)  
> /mnt/apk: 304848K total, 63168K used, 241680K available (block size 16384)  
> /data: 1963952K total, 141136K used, 1822816K available (block size 16384)  
> /dbdata: 71448K total, 1976K used, 69472K available (block size 4096)  
> /cache: 31336K total, 16K used, 31320K available (block size 2048)  
> /efs: 6064K total, 14K used, 6050K available (block size 1024)  
> /mnt/.lfs: Function not implemented  
> /mnt/sdcard: 13355840K total, 6223168K used, 7132672K available (block size 32768)  

Podemos notar a existência de um cartão de memória(**mmcblk0**) contendo três partições, mas somente
duas (mmcblk0p2 e mmcblk0p3) têm sistemas de arquivos montados! Por que será? O que aconteceu com **mmcblk0p1** ?

Agora vamos obter as variáveis de ambiente:

>leandro@leandro:~/Downloads/tablet$ **adb shell printenv**  
>DEFAULT_BASEIMAGE=/system/lib/dsp/baseimage.dof  
>ANDROID_ROOT=/system  
>LD_LIBRARY_PATH=/system/lib  
>PATH=/sbin:/system/sbin:/system/bin:/system/xbin  
>DSP_PATH=/system/lib/dsp  
>ASEC_MOUNTPOINT=/mnt/asec  
>INTERNAL_STORAGE=/mnt/sdcard  
>OTG_STORAGE=/mnt/sdcard/otg_disk  
>BOOTCLASSPATH=/system/framework/core.jar:/system/framework/ext.jar:/system/framework/framework.jar:/system/framework/android.policy.jar:/system/framework/services.jar  
>QOSDYN_FILE=/system/lib/dsp/qosdyn_3430.dll64P  
>ANDROID_BOOTLOGO=1  
>ANDROID_ASSETS=/system/app  
>EXTERNAL_STORAGE=/mnt/sdcard/external_sd  
>BT_FW_PATH=/system/lib/firmware  
>ANDROID_DATA=/data  
>PM_TBLFILE=/system/etc/policytable.tbl  
>TMPDIR=/data/local/tmp  
>ANDROID_PROPERTY_WORKSPACE=14,32768  

vários diretórios listados acima são conhecidos de quem mexe com gravação de ROM em Android.

E finalmente os dados de um dos arquivos mais importante do sistema:

>leandro@leandro:~$ **adb shell cat /system/build.prop**  
>\# begin build properties  
>\# autogenerated by buildinfo.sh  
>ro.build.id=FROYO  
>ro.build.display.id=FROYO.UJKM5  
>ro.build.version.incremental=UJKM5  
>ro.build.version.sdk=8  
>ro.build.version.codename=REL  
>ro.build.version.release=2.2.2  
>ro.build.date=Mon Jun 13 08:39:01 BRT 2011  
>ro.build.date.utc=1307965141  
>ro.build.type=user  
>ro.build.user=sidclei  
>ro.build.host=bldhp-4  
>ro.build.tags=release-keys  
>ro.product.model=GT-P1010  
>ro.product.brand=samsung  
>ro.product.name=GT-P1010  
>ro.product.device=GT-P1010  
>ro.product.board=GT-P1010  
>ro.product.cpu.abi=armeabi-v7a  
>ro.product.cpu.abi2=armeabi  
>ro.product.manufacturer=samsung  
>ro.product.locale.language=en  
>ro.product.locale.region=GB  
>ro.wifi.channels=  
>ro.board.platform=omap3  
>\# ro.build.product is obsolete; use ro.product.device  
>ro.build.product=GT-P1010  
>\# Do not try to parse ro.build.description or .fingerprint  
>ro.build.description=GT-P1010-user 2.2.2 FROYO UJKM5 release-keys  
>ro.build.fingerprint=samsung/GT-P1010/GT-P1010/GT-P1010:2.2.2/FROYO/UJKM5:user/release-keys  
>\# Samsung Specific Properties  
>ro.build.PDA=P1010UJKM5  
>ro.build.hidden_ver=P1010UJKM5  
>ro.build.changelist=1023452  
>ro.build.buildtag=  
>\# end build properties  
>\# system.prop for Latona FF  
>  
>ro.sf.lcd_density=240  
>ro.sf.hwrotation=90  
>  
>rild.libpath=/system/lib/libsec-ril-apalone.so  
>rild.libargs=-d /dev/ttyS0  
>wifi.interface=tiwlan0  
>jpeg.libskiahw.decoder.enable=0  
>jpeg.libskiahw.decoder.thresh=100000  
>  
>\# The OpenGL ES API level that is natively supported by this device.  
>\# This is a 16.16 fixed point number  
>ro.opengles.version=131072  
>  
>\# default heap size : default -> 48m  
>dalvik.vm.heapsize=48m  
>\#  
>\# ADDITIONAL_BUILD_PROPERTIES  
>\#  
>keyinputqueue.use_finger_id=true  
>windowsmgr.max_events_per_sec=60  
>keyinputqueue.use_finger_id=true  
>windowsmgr.max_events_per_sec=60  
>keyinputqueue.use_finger_id=true  
>windowsmgr.max_events_per_sec=60  
>ro.url.legal=http://www.google.com/intl/%s/mobile/android/basic/phone-legal.html  
>ro.url.legal.android_privacy=http://www.google.com/intl/%s/mobile/android/basic/privacy.html  
>ro.com.google.locationfeatures=1  
>ro.setupwizard.mode=DISABLED  
>ro.com.google.gmsversion=2.2_r10  
>media.stagefright.enable-player=false  
>media.stagefright.enable-meta=false  
>media.stagefright.enable-scan=false  
>media.stagefright.enable-http=false  
>media.stagefright.enable-record=false  
>keyguard.no_require_sim=true  
>ro.config.ringtone=Minimal_tone.ogg  
>ro.config.notification_sound=01_Sherbet.ogg  
>ro.config.alarm_alert=Good_Morning.ogg  
>ro.config.media_sound=Media_preview_Touch_the_light.ogg  
>ro.opengles.version=131072  
>net.bt.name=Android  
>dalvik.vm.stack-trace-file=/data/anr/traces.txt 

## Conclusão

Fui derrotado em minha primeira investida, mas saí com algum conhecimento extra sobre meu equipamento
e do funcionamento do Android como um todo!

Eu já sabia que o Android possuía algumas diferenças em relação às distribuições Linux de Desktop,
mas foi interessante vê-las ao vivo!

O desafio de instalar um novo sistema no meu tablet antigo mostrou-se mais desafiador do que parecia
a princípio, mas não desistirei tão cedo!

Mais adiante vou tentar cobrir o processo de boot completo do Android e talvez o sistema de arquivos dele
para ajudar quem quiser tentar fazer o mesmo que eu.




