---
title: Removendo pacotes órfãos
date: 2022-08-18
tags:
  - "ubuntu"
  - "linux"

categories:
  - "Linux"
  - "Dicas"
---

Quando um pacote é instalado, junto com ele chegam suas dependências. Mas quando ele é desinstalado, suas dependências permanecem dentro do sistema.

<!--more-->

Isso porque uma dependência pode ser requerida por outro programa e removê-la sem cuidado poderia levar a um desastre como, por exemplo,
desinstalar todo o ambiente gráfico! Pois uma dependência puxa outra...

Isso na opção padrão. Podemos orientar o gerenciador de pacotes a proceder diferentemente se quisermos.

Mas a dica de hoje é para controlar os pacotes órfãos, aqueles que foram instalados para servirem de pré-requisito para
outro mas que não foram removidos quando o pacote dependente foi removido e agora, não sendo usado nem referenciado por mais
ninguém, vagam pelo sistema de gestão de pacotes consumindo espaço em disco e entrada no banco de dados.

Eu vou testar o **deborphan**, uma aplicação de linha de comando que faz exatamente o que o nome sugere.

Primeiro vamos checar se essa aplicação está disponível para instalação em nosso repositório:

>leandro@leandro:~$ **apt-cache search deborphan**  
>deborphan - programa que pesquisa pacotes instalados e não utilizados, exemplo bibliotecas  

Certos do existência, vamos instalar:

>leandro@leandro:~$ **sudo apt-get install deborphan**

Vou executar sem argumentos e ver como será o resultado:

>leandro@leandro:~$ **deborphan**  
>&nbsp;&nbsp;&nbsp;&nbsp;android-tools-adb:all  
>&nbsp;&nbsp;&nbsp;&nbsp;android-tools-fastboot:all  
>&nbsp;&nbsp;&nbsp;&nbsp;lib32z1:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;libllvm9:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;libncurses5:i386  
>&nbsp;&nbsp;&nbsp;&nbsp;libncurses5-dev:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;libupnp6:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;wine-stable:all  

O **deborphan** não precisa ser executado com privilégio de administrador (root).

O resultado parece meio decepcionante. Imaginei que haveria centenas de entradas. 

Há um outro problema, que não chega a ser da aplicação. Eu achei a sugestão para esse comando navegando pela Internet e
nem lembro onde foi que achei originalmente a referência. Mas depois, pesquisando mais sobre o assunto, encontrei diversos
artigos sugerindo remover imediatamente esses pacotes. Algo como:

>leandro@leandro:~$ **deborphan | xargs sudo apt-get -y remove --purge**  

Isso parece coerente, mas analisando a saída para meu sistema, eu posso ver que há pacotes que eu efetivamente uso, como
o android-tools-adb! 

Ou seja, pode ser uma situação perigosa! Mas deixe-me consultar o gestor de pacotes sobre as três entradas  não relacionadas
a bibliotecas (**android-tools-adb:all**, **android-tools-fastboot:all**,**wine-stable:all**)

>leandro@leandro:~$ **apt show android-tools-adb**  
>Package: android-tools-adb  
>Version: 1:8.1.0+r23-5ubuntu2  
>Priority: extra  
>Section: universe/devel  
>Source: android-platform-system-core  
>Origin: Ubuntu  
>Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>  
>Original-Maintainer: Android Tools Maintainers <android-tools-devel@lists.alioth.debian.org>  
>Bugs: https://bugs.launchpad.net/ubuntu/+filebug  
>Installed-Size: 27,6 kB  
>Depends: adb  
>Homepage: https://android.googlesource.com/platform/system/core  
>Download-Size: 11,0 kB  
>APT-Manual-Installed: yes  
>APT-Sources: http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  
>Description: pacote de transição  
>&nbsp;**Este é um pacote de transição. Pode ser removido sem prejudicar o sistema.**  

>leandro@leandro:~$ **apt show android-tools-fastboot**  
>Package: android-tools-fastboot  
>Version: 1:8.1.0+r23-5ubuntu2  
>Priority: extra  
>Section: universe/devel  
>Source: android-platform-system-core  
>Origin: Ubuntu  
>Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>  
>Original-Maintainer: Android Tools Maintainers <android-tools-devel@lists.alioth.debian.org>  
>Bugs: https://bugs.launchpad.net/ubuntu/+filebug  
>Installed-Size: 27,6 kB  
>Depends: fastboot  
>Homepage: https://android.googlesource.com/platform/system/core  
>Download-Size: 4.060 B  
>APT-Manual-Installed: yes  
>APT-Sources: http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  
>Description: pacote de transição  
>&nbsp;**Este é um pacote de transição. Pode ser removido sem prejudicar o sistema.**  


>leandro@leandro:~$ **apt show wine-stable**  
>Package: wine-stable  
>Version: 3.0.1ubuntu1  
>Priority: optional  
>Section: universe/otherosfs  
>Source: wine1.6 (1:3.0.1ubuntu1)  
>Origin: Ubuntu  
>Maintainer: Graham Inggs <ginggs@ubuntu.com>  
>Bugs: https://bugs.launchpad.net/ubuntu/+filebug  
>Installed-Size: 8.192 B  
>Depends: wine  
>Download-Size: 2.064 B  
>APT-Manual-Installed: yes  
>APT-Sources: http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  
>Description: Windows API implementation (transitional package)  
>&nbsp;**This is a transitional dummy package to complete the migration to the Debian**  
>&nbsp;**wine packages. It can be safely removed.**  

Como podemos ver, removê-los é uma opção segura. Mas...

Quanto de espaço obteríamos de volta?

>leandro@leandro:~$ **deborphan -z**  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **27** android-tools-adb:all  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **27** android-tools-fastboot:all  
>&nbsp;&nbsp;&nbsp;&nbsp; **172** lib32z1:amd64  
>&nbsp;&nbsp; **67657** libllvm9:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp; **326** libncurses5:i386  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **6** libncurses5-dev:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp; **335** libupnp6:amd64  
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **8** wine-stable:all  

Olhando a saída do comando você pode ficar confuso. Não há indicação da unidade de medida referenciada. Mas comparando
com a saída do comando **apt show** que rodei anteriormente podemos ver que a unidade é **Kilobytes**(sim, kilobytes!!).

Ou seja, podemos recuperar **68558 Kb** ou cerca de **66Mb** de espaço em disco!!! Absolutamente decepcionante! Mas se
tivermos em conta que o pacote custou **359Kb** de espaço em disco até que é um bom negócio, mas acho que fazer esse tipo
de limpeza não deveria ser alvo de preocupação de ninguém. Bem, mas cada caso é um caso.

Um servidor de rede, por exemplo, que não costuma receber instalações frequentes de novas aplicações não deve se preocupar.
Talvez a máquina de um usuário mais arrojado possa se beneficiar. 

É claro que o programa também melhora a indexação dos pacotes no banco de dados do gestor de pacotes, mas dada a pequena
quantidade, não sei se causa impacto perceptível.

O comando tem várias opções que podem ser usadas para refinar o resultado ou modificar seu funcionamento. A MAN PAGE dele
é a melhor fonte de consulta para isso.

Se quisermos remover os pacotes todos de uma vez podemos rodar o comando que mostrei no início da dica

>leandro@leandro:~$ **deborphan | xargs sudo apt-get -y remove --purge**  
>Lendo listas de pacotes... Pronto  
>Construindo árvore de dependências       
>Lendo informação de estado... Pronto  
>Os seguintes pacotes foram instalados automaticamente e já não são necessários:  
>&nbsp;&nbsp;adb android-libadb android-libbacktrace android-libbase android-libboringssl android-libcrypto-utils android-libcutils android-libetc1  
>&nbsp;&nbsp;android-libf2fs-utils android-liblog android-libsparse android-libunwind android-libutils android-libziparchive android-sdk-platform-tools  
>&nbsp;&nbsp;android-sdk-platform-tools-common dmtracedump etc1tool fastboot hprof-conv jsonlint libtinfo5:i386 php-composer-ca-bundle  
>&nbsp;&nbsp;php-composer-semver php-composer-spdx-licenses php-composer-xdebug-handler php-json-schema php-symfony-console php-symfony-filesystem  
>&nbsp;&nbsp;php-symfony-finder php-symfony-process sqlite3 squashfs-tools  
>**Utilize 'sudo apt autoremove' para os remover.**  
>Os pacotes a seguir serão REMOVIDOS:  
>&nbsp;&nbsp;android-tools-adb android-tools-fastboot lib32z1 libllvm9 libncurses5:i386 libncurses5-dev libupnp6 wine-stable  
>0 pacotes atualizados, 0 pacotes novos instalados, 8 a serem removidos e 52 não atualizados.  
>Depois desta operação, 70,2 MB de espaço em disco serão liberados.  
>(Lendo banco de dados ... 271907 ficheiros e directórios actualmente instalados.)  
>A remover android-tools-adb (1:8.1.0+r23-5ubuntu2) ...  
>A remover android-tools-fastboot (1:8.1.0+r23-5ubuntu2) ...  
>A remover lib32z1 (1:1.2.11.dfsg-2ubuntu1.3) ...  
>A remover libllvm9:amd64 (1:9.0.1-12) ...  
>A remover libncurses5:i386 (6.2-0ubuntu2) ...  
>A remover libncurses5-dev:amd64 (6.2-0ubuntu2) ...  
>A remover libupnp6 (1:1.6.19+git20160116-1) ...  
>A remover wine-stable (3.0.1ubuntu1) ...  
>A processar 'triggers' para libc-bin (2.31-0ubuntu9.9) ...  

Muito bom, de quebra a desinstalação nos deu mais uma dica para limpar o sistema.

> sudo apt autoremove

Que se eu seguir pode provocar o caos na minha máquina! Na verdade ali estão referenciados muitos pacotes que eu efetivamente
uso no dia a dia. Por exemplo, o pacote **android-tools-adb** 

leandro@leandro:~$ apt depends  android-tools-adb  
>android-tools-adb  
>&nbsp;&nbsp;Depende: adb  

O pacote adb foi instalado como dependência do pacote **android-tools-adb** e o autoremove entende que ele não é mais necessário, quando é necessário sim!!!!

Isso acontece porque os pacotes acima foram instalados automaticamente como dependência. O autoremove vai fazer a limpeza
com base nesse critério.

Para corrigir isso eu vou marcar

>leandro@leandro:~$ **sudo apt-mark auto adb android-libadb android-libbacktrace android-libbase android-libboringssl android-libcrypto-utils android-libcutils android-libetc1**  
>adb marcado para manter.  
>android-libadb marcado para manter.  
>android-libbacktrace marcado para manter.  
>android-libbase marcado para manter.  
>android-libboringssl marcado para manter.  
>android-libcrypto-utils marcado para manter.  
>android-libcutils marcado para manter.  
>android-libetc1 marcado para manter.  

Agora é como se eu tivesse instalado esses pacotes pela linha de comando, em vez de terem sido instalados como dependência.
Isso deve impedir o autoremove de agir desavisadamente. Ou seja, para esse pacote sair do sistema eu devo removê-lo explicitamente!

É claro que você também pode apenas instalar de novo se porventura esquecer e os pacotes forem removidos. É apenas para
não precisar ficar vigiando o **apt autoremove** a toda hora.

Os demais pacotes que apareceram eu entendi que eram indesejáveis mesmo e deixei serem removidos.

## Conclusão

Não saia rodando qualquer comando que encontre pela internet! 

O **deborphan** pesquisa pelo banco de dados do gerenciador de pacotes e filtra aqueles que não possuam mais outros
pacotes que dependam deles.

Mas isso não significam que seja seguro removê-los. É bom fazer uma pesquisa usando o comando **apt** antes para se
certificar da segurança em remover o pacote.

Outro ponto a se questionar é se o benefício obtido vai compensar a instalação e o tempo gasto em aprender sobre o comando.
Achei o resultado decepcionante, mas isso pode não ser o mesmo para todos!

Mas vale a pena conhecer sobre a ferramenta ao menos como opção de limpeza de sistemas. 





