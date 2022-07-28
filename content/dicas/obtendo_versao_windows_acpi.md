---
title: Obtendo a versão do Windows na BIOS/EFI
date: 2022-07-28
tags:
  - "ubuntu"
  - "linux"
categories:
  - "Linux"
---
Uma BIOS/EFI com suporte a ACPI pode requisitar informação sobre qual sistema operacional está 
executando e, se for o caso, bloquear tentativa de acesso a recursos.

Isso pode ser conveniente quando o fabricante não implementa funcionalidades e deseja lançar o
produto rapidamente no mercado.
 
Neste caso, a BIOS espera que apenas algumas versões de algum sistema operacional(Windows?) esteja instalado. 
Isso é muito comum em notebooks e vamos ver como descobrir qual sistema operacional nossa BIOS(EFI) espera encontrar.
<!--more-->
Para isso vou acessar as tabelas ACPI da BIOS/EFI. Elas fornecema interface entre o sistema operacional e o firmware do sistema.

A tabela que eu quero é a DSDT. Essa é a tabela principal na parte ACPI do BIOS(EFI) e define a maioria dos dispositivos do
sistema.

Por padrão, o bootloader transfere as tabelas OEM (de fabricante) do BIOS(EFI) para o sistema operacional.  No caso do linux,
a tabela estará no subsistema **firmware**, mas especificamente em **/sys/firmware/acpi/tables/DSDT**

Esse arquivo está no formato binário, mas usando o comando **strings** (que exibe os caracteres - texto - dentro do arquivo), podemos
extrair o que queremos.

É bom lembrar também que esse arquivo é de propriedade do usuário **root** e que não teremos permissão de leitura como usuário normal, sendo
necessário usar **sudo** (ou acessar o terminal como root se a distribuição não disponibilizar esse comando).

Sendo assim, para qual versão do sistema operacional Windows meu laptop foi feito para funcionar?

>leandro@leandro:~$ **sudo strings  /sys/firmware/acpi/tables/DSDT | grep -i windows**  
>Windows 2012  
>Windows 2001  
>Windows 2001 SP1  
>Windows 2001 SP2  
>Windows 2001.1  
>Windows 2006  
>Windows 2009  
>Windows 2012  

Pegamos a versão mais recente que aparecer na listagem - Windows 2012 - assim eu espero que meu laptop seja compatível 
no máximo com Windows 8 e Windows Server 2012! 

A listagem de compatibilidade pode ser encontrada [aqui](https://docs.microsoft.com/en-us/windows-hardware/drivers/acpi/winacpi-osi)

Em sistemas operacionais que não estejam na lista, pode ser que a BIOS desabilite algum recurso. 

No caso do Linux, podemos indicar ao firmware durante o boot para personalizar um sistema windows compatível passando o parâmetro
**acpi_osi** para o kernel. Para isso obtemos a versão Windows e passamos algo como:

> acpi_osi='Windows 2012'

ou

> acpi_osi=! acpi_osi='Windows 2012' 

Assim estamos informando ao BIOS/ACPI que nosso sistema é um Windows 8 ou Windos Server 2012.

Podemos fazer isso no menu do grub durante o boot ou configurar de forma permanente no sistema operacional no arquivo
**/etc/default/grub**

Em algumas máquinas (não todas e nem foi o meu caso) esse truque elimina algumas mensagens de 
erro relacionandos a ACPI durante o boot e que podem ser consultadas posteriormente com o comando **dmesg**. 

Se a versão mais recente não funcionar, você pode testar as anteriores e talvez alguma funcione.
