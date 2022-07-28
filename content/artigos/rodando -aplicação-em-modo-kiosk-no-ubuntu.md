---
title: Rodando um aplicativo em modo Kiosk no Ubuntu Linux 20.04
date: 2022-05-30
tags:
  - "ubuntu"
  - "linux"
  - "kiosk"	
categories:
  - "Linux"
---

O modo kiosk (ou quiosque) é um modo de operação que disponibiliza para o usuário um único aplicativo ou aplicação em tela cheia e sem bordas ou barras de ferramentas. Seu principal uso é nos totens de auto atendimento, como os caixas automáticos e filas de atendimento.
<!--more-->

Nesse modo de operação o usuário interage com um único aplicativo,isto é, não possui acesso aos demais recursos do sistema operacional. Além disso, o sistema não pode
hibernar por ociosidade ou desligar o monitor. A funcionalidade de proteção de tela também deve ser desabilitada, mas, por vezes, esse recurso é habilitado para 
mostrar alguma mensagem de propaganda.

## Iniciando sem Interface Gráfica
A primeira coisa que devemos saber é que teremos de desabilitar o ambiente desktop, para isso vamos configurar o sistema operacional para iniciar sem interface gráfica, mas com suporte à conexão em rede 
(Runlevel 3 ou target multi-user na linguagem systemd)

> sudo systemctl set-default multi-user.target

Nossa aplicação de exemplo será um menu construído em html disponível localmente no próprio HD, mas poderia ser um site da web ou um sistema na rede local. Nós vamos abrir esse
site com o firefox. Caso não o tenha instalado, você pode rodar:

> sudo apt install firefox

é necessário passar o caminho completo para o firefox, para evitar qualquer problema relacionado à variável PATH.
Para encontrar o caminho para o binário, você pode rodar:

> leandro@leandro:~$ whereis firefox  
> firefox: /usr/bin/firefox /usr/lib/firefox /etc/firefox /usr/share/man/man1/firefox.1.gz

Como podemos ver acima, o firefox está instalado em /usr/bin/

## Configurando a aplicação

Precisaremos criar um arquivo de nome .xinitrc dentro do diretório home do usuário. Esse é um arquivo de configuração para o xinit e o startx. A Wikipedia nos informa que:
> "O programa xinit permite que um usuário inicie manualmente um servidor de exibição Xorg. O script startx(1) é um front-end para xinit(1)."

 Esse arquivo não pode ficar em qualquer lugar, nem pode ter qualquer nome ou extensão. Para criar adicionando conteúdo usando o editor Nano, execute
> nano ~/.xinitrc

e insira o conteúdo abaixo
>#!/usr/bin/env bash  
> xset -dpms  
> xset s off  
> xset s noblank  
> unclutter &  
> exec /usr/bin/firefox --kiosk -width 800 -height 600 -url ~/menu.html  

Os Comandos **xset** vão configurar ajustes no monitor. Coisas como desabilitar stand-by, desabilitar proteção de tela e impedir que a tela fique inteiramente branca por ociosidade.

A quinta linha chama o commando unclutter em background. Esse comando permite ocultar o ponteiro do mouse depois de um tempo sem interação e deixa nossa aplicação mais, digamos, charmosa. 

Esse comando não vem instalado por padrão, então, para adicioná-lo ao
sistema faça:

> sudo apt install unclutter

A últimalinha chama o firefox em modo quiosque com tamanho de 800 pixels de comprimento por 600 pixels de altura e abrindo 
o arquivo menu.html que está na pasta home do usuário.O comando exec substitui o processo do shell pelo do Firefox. Isso é para evitar que em caso de algum erro inesperado o usuário receba um shell de presente.

Salve o arquivo (ctrl+O). Como não estaremos usando um ambiente desktop (como KDE, Xfce, Gnome etc) não teremos as informações relacionadas à tela. Por isso adotamos uma resolução conservadora de 800x600, suportada por quase 
todos os monitores.

Precisamos também modificar o arquivo .bashrc que é executado após a autenticação para configurar o ambiente. Novamente abra usando o editor Nano:

> nano .bashrc

e adicione o seguinte trecho no final do arquivo:

> if [[ !$DISPLAY && $XDG_VTNR -eq 1 ]]; then  
>    exec startx  
> fi  

É necessário muita atenção para não modificar outras partes deste arquivo, pois ele é de fundamental importância para o funcionamento correto do sistema.
Aperte ctrl+O para salvar a modificação.

## Configurando autologin em modo texto
Agora precisamos configurar o sistema para fazer autologin, isto é, logar automaticamente em uma conta de usuário sem necessidade de informar a senha pelo teclado. O mesmo recurso presente nos ambientes gráficos, mas aqui em modo texto.

Vamos editar o arquivo de configuração do serviço getty@tty1, que é responsável por criar e gerenciar os terminais em modo texto (geralmente disponíveis entre F1 e F6). Para ver o status deste serviço no seu sistema, rode:

>leandro@leandro:~$ sudo systemctl status  getty@tty1  
>● getty@tty1.service - Getty on tty1  
>     Loaded: loaded (/lib/systemd/system/getty@.service; enabled; vendor preset: enabled)  
>     Active: active (running) since Fri 2022-05-27 21:35:50 -03; 45min ago  
>       Docs: man:agetty(8)  
>             man:systemd-getty-generator(8)  
>             http://0pointer.de/blog/projects/serial-console.html  
>   Main PID: 811 (agetty)  
>      Tasks: 1 (limit: 4499)  
>     Memory: 288.0K  
>     CGroup: /system.slice/system-getty.slice/getty@tty1.service  
>             └─811 /sbin/agetty -o -p -- \u --noclear tty1 linux  
>  
>mai 27 21:35:50 leandro systemd[1]: Started Getty on tty1.  


Você vai precisar obter o nome de login do usuário que será logado automaticamente, para isso rode:

> leandro@leandro:~$ whoami  
> **leandro**  


Vamos configurar o autologin rodando o comando:

> sudo systemctl edit getty@tty1

Isso vai abrir o editor de texto configurado como padrão no sistema. No meu sistema é o Nano, na sua instalação pode não ser. Caso queira conferir qual o editor configurado ou mesmo alterar a opção, rode o **update-alternatives**:

>leandro@leandro:~/projetos/site/leandropecanhascardua.github.io$ update-alternatives --config editor  
>Existem 3 escolhas para a alternativa editor (disponibiliza /usr/bin/editor).  
>  
>  Selecção   Caminho            Prioridade Estado  
> \----------------------------------------------------------------------------------------------------------  
>\* 0            /bin/nano&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  40&nbsp;     modo automático  
>  1            /bin/ed&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  -100&nbsp;       modo manual  
>  2            /bin/nano &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 40&nbsp;&nbsp;     modo manual  
>  3            /usr/bin/vim.tiny   15 &nbsp;      modo manual  
>  
>  
> Pressione <enter> para manter a escolha actual[*], ou digite o número da selecção:   

Basta digitar um número que esteja na coluna "Seleção" e a modificação estará feita. Apenas apertando Enter o programa encerra a execução sem fazer nenhuma alteração.

 Adicione o conteúdo abaixo com o cuidado de não digitar errado.

>[Service]  
>ExecStart=  
>ExecStart=-/usr/sbin/agetty  --autologin leandro --noclear %I $TERM  

Apertando ctrl+O e a configuração estará salva (se o editor padrão do sistema for o Nano). No nosso caso, configuramos o autologin no usuário leandro (por exemplo).

Se o serviço getty@tty1 não estiver habilitado, habilite-o:

> sudo systemctl enable getty@tty1

Se porventura alguma coisa sair errado e for necessário voltar à configuração padrão do serviço getty@tty1. Rode

> sudo systemctl revert getty@tty1

## Criando uma aplicação de teste
crie um arquivo menu.html com o sistema a ser executado. Como exemplo eu fiz o seguinte:
```html
 <html>  
 <head>  
 <style>  
 .item_menu {  
	width:40%;  
	height:30%;  
	margin-bottom:5%;  
	display:flex;  
	justify-content: center;  
	align-items: center;  
}  
a:link {  
	color:white;  
	text-decoration: none;  
}  
a:hover {  
	text-decoration: bold;  
}  
 </style>  
 </head>  
 <body style="width:785px;height:570px;margin:auto;text-align:center;">  
	<div style="height:20%;">  
		<h1 >Terminal de Auto Atendimento </h1>  
	</div>  
	<div >  
		<div style="background-color:SteelBlue;float:left;" class="item_menu">  
			<a href="#" >Segunda Via da Conta </a>  
		</div>    
		<div style="background-color:DeepSkyBlue;float:right;"  class="item_menu">  
			<a href="#" >Consulta de Protocolo</a>  
		</div>    
		<div style="background-color:ForestGreen;float:left;float:right;"  class="item_menu">  
			<a href="#" >Religação de Instalação</a>  
		</div>    
		<div style="background-color:DarkOrange;"  class="item_menu">  
			<a href="#" >Rede Credenciada</a>  
		</div>  
	</div>  
</body>  
</html>  
```
A partir daqui o sistema pode ser reiniciado e testado. Mas é importante lembrar que em caso de erro pode ser que o sistema fique inacessível (principalmente testando numa máquina virtual). 

É uma boa ideia instalar um servidor ssh e acessar por meio dele. Para encerrar a aplicação e Para voltar à linha de comando na máquina totem, rode:

> kilall firefox


## Considerações Finais

O modo quiosque é um recurso pouco explorado no ambiente Linux, mas amplamente usado em vários sistemas e aplicações dos mais variados ramos e atividades.


Nosso exemplo usou o Firefox e uma página html simples para simular um sistema de autoatendimento. 

O resultado pode ter deixado a desejar um pouco, principalmente no quesito performance. Achei o tempo de carregamento muito alto. Mas se levarmos em conta que usei a instalação completa do  XUbuntu e que o firefox é um comedor de memória , saio satisfeito com o resultado.
Há um grande potencial de melhoria!

Rodar o exemplo para outra distribuição implica em modificar algumas partes do exemplo,pois nem todas as ditros usam SystemD, mas a substituição do Firefox ou o uso de uma
aplicação própria construída somente para isso pode indicar novos rumos.

No material de consulta eu vi que é possível enxugar o sistema instalando apenas alguns pacotes-chave. Talvez eu refaça este exemplo em uma distribuição Ubuntu Minimal como teste de hipótese no futuro.
Outro ponto interessante a ressaltar é a necessidade de instalar um servidor ssh na máquina, visto que um possível erro pode deixar o sistema operacional inacessível para correção. Assim, um servidor ssh é recomendável, ou saber acessar o sistema pelo menu do **GRUB**.

Enquanto eu escrevia esse capítulo final eu li sobre usar o modo quisque para rodar jogos sem o peso de um ambiente gráfico. Eu confesso que me senti desafiado a tentar fazer essa adaptação, mas, por ora, este aqui já é suficiente
e há outros assuntos em pauta para os próximos artigos. 

#### Fontes:

https://wiki.archlinux.org/title/Xinit_(Portugu%C3%AAs)

https://sylvaindurand.org/launch-chromium-in-kiosk-mode/

