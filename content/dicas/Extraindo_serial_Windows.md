---
title: Extrair o serial do Windows a partir da BIOS/EFI
date: 2022-08-07
tags:
  - "ubuntu"
  - "linux"

categories:
  - "Linux"
  - "Dicas"
---
Em outra [dica](/dicas/obtendo_versao_windows_acpi/) eu mostrei como obter a versão do sistema operacional Windows que um notebook foi preparado para rodar.

Agora é hora de extrair a chave de validação do Windows(serial) que o fabricante colocou na BIOS/EFI. Sim, o serial está na
BIOS/EFI e está acessível para você, comprador do equipamento!
<!--more-->
É necessário ressaltar que esse serial é inserido quando se compra uma máquina que venha com Windows pré-instalado.

Basta usar o comando **strings** como usuário root ( já que o arquivo é restrito):

>leandro@leandro:~$ **sudo strings /sys/firmware/acpi/tables/MSDM | tail -n 1**  
>D6N96-4F3W9-KV7TT-MK4GP-***  

O arquivo acessado faz parte da tabela **ACPI** exportada dentro do sistema de arquivos virtual **/sys**

Outras dicas na Internet vão ensinar a extrair esse serial via **acpidump** e também é um método válido e eficaz. Este aqui
tem a vantagem de não precisar instalar nenhuma ferramenta adicional.
