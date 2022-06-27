---
title: Remover App de operadora Claro ou Vivo do celular Android usando Linux
date: 2022-06-26
tags:
  - "Ubuntu"
  - "linux"
  - "terminal"	
  - "android"
  - "celular"
categories:
  - "Linux"
  - "Dicas"
---
Todo chip de operadora de telefonia no Brasil instala junto consigo um App. Geralmente é apenas um aplicativo
de relacionamento com cliente, para oferecer ofertas promocionais e descontos em pacotes.

Só que, de vez em quando, esses aplicativos se tornam invasivos, abrindo um menu pop-up a todo
momento de forma inconveniente ou sem controle, aborrecendo o usuário, principalmente clientes de planos pré-pagos.

<!--more-->
O pobre(!) do usuário tenta de todas as formas configurar para não receber notificações, desabilita
tudo o que pode no aparelho, mas é tudo inútil, as mensagens não param de forma alguma!

Só nos resta remover o aplicativo do celular, e é isso o que faremos nesta dica usando o terminal
de comandos do Ubuntu Linux. 

Não é necessário fazer root do aparelho, se você usa Linux, **você é o root**!!


### Habilitar opções do desenvolvedor no aparelho celular 
Primeiramente é necessário configurar algumas opções no aparelho celular Android que vêm
desabilitadas por padrão. 

Vá nas configurações do Aparelho e selecione Opções do desenvolvedor.

![Opções do desenvolvedor](/img/post/dica03/config01.png)

Essa opção também pode estar desabilitada no aparelho, então é necessário pesquisar como habilitar
essa opção no modelo em mãos. No meu aparelho é necessário clicar 07 vezes no item 
**número de compilação** que fica dentro do item **Sobre o Telefone**(como mostrado na imagem acima), 
mas pode ser diferente em outro aparelho.

É necessário ativar as opções do desenvolvedor e em seguida habilitar a **Depuração Usb**.

![Ativar opções do desenvolvedor](/img/post/dica03/config02.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![Habilitar depuração usb](/img/post/dica03/config03.png)

Feito isso podemos seguir adiante.

### Descobrir o nome do app da operadora Claro ou da Vivo
Curiosamente, este foi o passo mais complicado. É que os aplicativos das operadoras não têm
nomes significativos. Não podem ser encontrados apenas fazendo uma listagem. Para isso utilizei
um App auxiliar, o [App Inspector](https://play.google.com/store/apps/details?id=com.ubqsoft.sec01&hl=pt_BR&gl=US)

Uma vez instalado no celular, procuramos pelo aplicativo que nos interessa:

![App da Vivo](/img/post/dica03/app_inspector_01.png)

E uma vez encontrado, pegamos o nome do pacote no sistema.

![Nome do pacote do App da Vivo](/img/post/dica03/app_inspector_02.png)

### Instalar o pacote adb

Tendo o nome, agora sim podemos exorcizar o demônio. Para isso vamos usar os comandos do pacote adb.

O pacote **adb** fornece ferramentas de linhas de comando para fazer a comunicação do computador com uma
instância de emulador ou com um dispositivo Android conectado.

Em sistemas baseados em apt podemos fazer: 

> leandro@leandro:~$ sudo apt install adb android-tools-adb

### Verificar se o celular está sendo encontrado
Nesta etapa conectamos o celular Android ao computador via cabo usb. E verificamos se ele
está sendo encontrado:

>leandro@casa:~$ adb devices  
>List of devices attached  
>RQ8M803RL4X	device  

Lembrando que não precisamos executar os comandos como root do sistema Linux, apenas como o 
usuário normal do dia a dia.

### Encontrar o aplicativo
Agora listamos os aplicativos instalados no celular e filtramos para confirmar que o aplicativo
realmente tem o nome que obtivemos no App Inspector.

>leandro@casa:~$ adb shell pm list packages |grep stk  
>package:com.android.stk  
>package:com.android.stk2  

O primeiro é o App da Claro(Menu Claro) e o segundo é o Vivo Chip.

### Remover aplicativo Menu claro(ou Vivo Chip)
Agora sim o tiro de misericórdia:

>leandro@casa:~$ adb uninstall --user 0 com.android.stk  
>Success  

remover aplicativo Vivo Chip
>leandro@casa:~$ adb uninstall --user 0 com.android.stk2  
>Success  

**Fonte:**

https://stackpointer.io/mobile/android-adb-list-installed-package-names/416/

https://www.vivaolinux.com.br/topico/Android/Como-remover-app-do-android-ROOT-usando-linux

https://www.fosslinux.com/25170/how-to-install-and-setup-adb-tools-on-linux.htm

