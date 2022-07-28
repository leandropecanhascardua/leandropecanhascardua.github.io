---
title: Encontrando a senha da rede wifi gravada no Ubuntu Linux
date: 2022-06-08
tags:
  - "ubuntu"
  - "linux"
  - "terminal"	
  - "wifi"	
categories:
  - "Linux"
---
Esta é uma dica rápida e fruto de uma necessidade comum: Configurar a senha da rede wifi em um novo dispositivo. 

Não se trata de hackear a senha, ou seja, descobrir a senha para a qual não temos acesso legítimo. Trata-se de recuperar a senha armazenada em um dispositivo configurado
que acessa uma rede wifi para a qual temos acesso legítimo.
<!--more-->
O Ubuntu, assim como o Linux Mint usa o NetworkManager como daemon(serviço) de gerenciamento de rede(configuração e operação) tentando tornar as coisas tão fáceis quanto
for possível.

Os arquivos de configuração ficam dentro de /etc/NetworkManager e dentro deste diretório há um subdiretório system-connections, onde ficam registradas as configurações de
conexão. 

>leandro@leandro:~$ ls -l /etc/NetworkManager/system-connections/  
>total 4  
>-rw------- 1 root root 344 mar  7 16:32 VIVO-****.nmconnection  


O arquivo VIVO-****.nmconnection é a configuração da minha rede Wifi. Assim, um simples grep executado com permissão especial de administrador pode retornar

>leandro@leandro:~$ sudo grep -r '^psk=' /etc/NetworkManager/system-connections/  
>/etc/NetworkManager/system-connections/VIVO-****.nmconnection:psk=63A11*****  




