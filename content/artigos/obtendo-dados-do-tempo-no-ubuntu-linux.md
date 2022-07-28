---
title: Obtendo dados meteorológicos no Linux
date: 2022-05-18
tags:
  - "ubuntu"
  - "linux"
  - "meteorologia"
  - "inxi"
categories:
  - "Linux"
---
Uma das ferramentas mais versáteis do Linux é o inxi. Desenvolvida para fornecer detalhes sobre a configuração do sistema para fóruns de suporte, ela pode ir muito além
e informar também dados meteorológicos como temperatura, umidade relativa do ar, pressão atmosférica entre outros, tudo pela linha de comando.
<!--more-->

Esse comando vem instalado por padrão no Ubuntu Linux 20.4, mas pode não ser o caso em outras distribuições. Para sistemas baseados no gerenciador de pacotes apt, é possível
chegar o status do pacote com o comando


>leandro@leandro:~$ apt-cache policy inxi  
>inxi:  
>  **Instalado: 3.0.38-1-0ubuntu1**  
>  Candidato: 3.0.38-1-0ubuntu1  
>  Tabela de versão:  
> *** 3.0.38-1-0ubuntu1 500  
>        500 http://br.archive.ubuntu.com/ubuntu focal/universe amd64 Packages  
>        500 http://br.archive.ubuntu.com/ubuntu focal/universe i386 Packages  
>        100 /var/lib/dpkg/status  




Como podemos ver pela saída do comando acima, o pacote está instalado em nosso sistema na versão 3.0.38. Se porventura o pacote não estiver instalado no sistema, execute:

>sudo apt install inxi

É importante destacar que o recurso de dados meteorológicos que vamos explorar pode estar desabilitada pelo sistema operacional, então consulte o manual para mais informações.

## Uso Básico

O comando inxi executado apenas com a opção -w retorna a previsão do tempo para o dia, o que significa que o comando pode retornar o mesmo resultado ao longo do dia, ou seja, o resultado pode não ser a 
situação atual no momento da consulta. Tudo depende da fonte da pesquisa (que será visto mais adiante):

>leandro@leandro:~$ inxi -w  
>Weather:   Temperature: 25 C (77 F) Conditions: Few clouds Current Time: 2022-05-21 16:06:32 (America/Sao_Paulo)  
>           Source: WeatherBit.io  

Como podemos ver, a consulta retorna a temperatura, as condições de visibibilidade do céu, o horário, o local e também a fonte de consulta. A consulta é feita
pela internet, então é necessário uma conexão ativa e funcional com a internet para o funcionamento correto.

Em sua execução padrão (fonte de pesquisa padrão), a consulta é realizada tomando o endereço IP público e isso pode implicar em uma cidade diferente daquela real da máquina. 

## Pesquisa por Cidade

Se quisermos consultar as condições em uma cidade diferente, como por exemplo a cidade onde realmenteestou, é possível informá-la usando a opção -W. Neste
caso é necessário informar o país também. Para o Brasil deve-se usar código **br**:

>leandro@leandro:~$ inxi -W vitoria,br  
>Weather:   Temperature: 28 C (82 F) Conditions: Few clouds Current Time: 2022-05-21 16:16:47 Source: WeatherBit.io  
>leandro@leandro:~$  

Nomes de cidade não devem conter espaços. Se houver, substitua pelo caracter +. Por exemplo:

>leandro@leandro:~$ inxi -W vila+velha,br  
>Weather:   Temperature: 24 C (75 F) Conditions: Few clouds Current Time: 2022-05-21 16:20:10 Source: WeatherBit.io  

Essa opção (-W) permite que a busca seja feita por código postal e cidade/estado, mas esse recurso só está disponível nos Estados Unidos, ou seja, usar esses parâmetros 
no Brasil não é garantia de obter um resultado correto ou consistente. Não é permitido usar acentuação nos nomes de cidades (usar somente caracteres ASCII):
Por exemplo:

>leandro@leandro:~$ inxi -W vitoria,br  
>Weather:   Temperature: 28 C (82 F) Conditions: Few clouds Current Time: 2022-05-21 16:22:39 Source: WeatherBit.io  

Há três níveis de detalhe que podem ser retornados e que podem depender da fonte de dados adotada! Por exemplo:

>leandro@leandro:~$ inxi -x -W vila+velha,br  
>Weather:   Temperature: 24 C (75 F) Conditions: Few clouds Wind: from SSE at 6.7 m/s (24 km/h, 15 mph) Humidity: 64%  
>           Pressure: 1020.5 mb (34 in) Current Time: 2022-05-21 16:25:45 Source: WeatherBit.io  

Veja que ao relatório de saída agora foram adicionadas informações sobre velocidade e direção do vento, humidade relativa do ar e pressão atmosférica.

>leandro@leandro:~$ inxi -xx -W vila+velha,br  
>Weather:   Temperature: 24 C (75 F) Conditions: Few clouds Wind: from SSE at 6.7 m/s (24 km/h, 15 mph) Cloud Cover: 25%  
>           Humidity: 64% Dew Point: 16.8 C (62 F) Pressure: 1020.5 mb (34 in) Current Time: 2022-05-21 16:28:20  
>           Source: WeatherBit.io  
>leandro@leandro:~$  

Veja que agora também aparecem uma taxa aproximada de cobertura de nuvens no céu e o ponto de condensação da água (Dew Point).

>leandro@leandro:~$ inxi -xxx -W vila+velha,br  
>Weather:   Temperature: 24 C (75 F) Conditions: Few clouds Wind: from SSE at 6.7 m/s (24 km/h, 15 mph) Cloud Cover: 25%  
>           Humidity: 64% Dew Point: 16.8 C (62 F) Pressure: 1020.5 mb (34 in) Location: Vila velha, Br  
>           Current Time: 2022-05-21 16:31:17 Observation Time: 2022-05-21 16:00:00 (America/Sao_Paulo -0300)  
>           Source: WeatherBit.io  
>leandro@leandro:~$  

E esse é o relatório completo. Foi adicionada a hora da observação e a hora da observação, o que nos indica que os dados não são em tempo real.

Um problema que notei foi que, não havendo um meio de informar o Estado brasileiro na consulta, não há como desambiguar um nome, isto é, não há como distinguir
entre duas cidades com o mesmo nome mas em Estados diferentes, como é o caso de Vila Velha(ES) e Vila Velha(PR).

## Formato de Saída

Por padrão os relatórios mostram as medidas em unidades do Sistema Métrico Decimal(Celsius, Km/h) seguuida do Sistema Métrico Imperial Britânico(Fahrenhit, mph).
É possível também mudar as unidades de medição usando o parâmetro --weather-unit. As opções são unidade métrica(m), imperial(i), métrica-imperial(mi) e 
imperial-métrica(im),sendo métrica-imperial o formato padrão. Por exemplo:

>leandro@leandro:~$ inxi  -xxx -W vila+velha,br --weather-unit m  
>Weather:   Temperature: 24 C Conditions: Few clouds Wind: from SSE at 6.7 m/s (24 km/h) Cloud Cover: 25% Humidity: 64%  
>           Dew Point: 16.8 C Pressure: 1020.5 mb Location: Vila velha, Br Current Time: 2022-05-21 16:39:28  
>           Observation Time: 2022-05-21 16:00:00 (America/Sao_Paulo -0300) Source: WeatherBit.io  

Na saída acima, as medidas aparecem exclusivamente no Sistema Métrico Decimal. Fazendo a mesma consulta, mas solicitando a resposta exclusivamente no Sistema Métrico 
Imperial teríamos:

>leandro@leandro:~$ inxi  -xxx -W vila+velha,br --weather-unit i  
>Weather:   Temperature: 75 F Conditions: Few clouds Wind: from SSE at 15 mph Cloud Cover: 25% Humidity: 64% Dew Point: 62 F  
>           Pressure: 34 in Location: Vila velha, Br Current Time: 2022-05-21 16:40:49  
>           Observation Time: 2022-05-21 16:00:00 (America/Sao_Paulo -0300) Source: WeatherBit.io  

E finalmente a medida no Sistema Métrico Imperial seguida do sistema Métrico Decimal:
>leandro@leandro:~$ inxi  -xxx -W vila+velha,br --weather-unit im  
>Weather:   Temperature: 75 F (24 C) Conditions: Few clouds Wind: from SSE at 15 mph (6.7 m/s, 24 km/h) Cloud Cover: 25%  
>           Humidity: 64% Dew Point: 62 F (16.8 C) Pressure: 34 in (1020.5 mb) Location: Vila velha, Br  
>           Current Time: 2022-05-21 16:56:00 Observation Time: 2022-05-21 16:00:00 (America/Sao_Paulo -0300)  
>           Source: WeatherBit.io  

## Fonte de Consulta

Por padrão a consulta é feita usando a API do site WeatherBit.io, mas por vezes o limite de consultas é alcançado ou o site está indisponível. Nestas situações, pode-se modificar a fonte de pesquisa
através do parâmetro --weather-source, que pode ter valores de 1 a 9 onde geralmente os valores de 1 a 4 estão ativos.

>leandro@leandro:~$ inxi  -xxx -W vila+velha,br --weather-source 1  
>Weather:   Temperature: 23.97 C (75 F) Conditions: few clouds Wind: from SSE at 6.7 m/s (24 km/h, 15 mph) Cloud Cover: 20%  
>           Humidity: 64% Pressure: 1021 mb (34 in) Location: Vila velha, Br Current Time: 2022-05-21 17:01:40  
>           Observation Time: N/A **Source: OpenWeatherMap.org**  

Como visto, a opção 1 fez a consulta no site OpenWeatherMap.org. 

>leandro@leandro:~$ inxi  -xxx -W vila+velha,br --weather-source 2
>Weather:   Temperature: 24 C (75 F) Conditions: Few clouds Wind: from SSE at 6.7 m/s (24 km/h, 15 mph) Cloud Cover: 25%
>           Humidity: 64% Dew Point: 16.8 C (62 F) Pressure: 1020.5 mb (34 in) Location: Vila velha, Br
>           Current Time: 2022-05-21 17:03:19 Observation Time: 2022-05-21 16:00:00 (America/Sao_Paulo -0300)
>           **Source: WeatherBit.io**

A opção 2 é a fonte de pesquisa padrão, o site WeatherBit.io.

>leandro@leandro:~$ inxi  -xxx -W vila+velha,br --weather-source 3  
>Weather:   Temperature: 24 C (75 F) Conditions: Partly cloudy Wind: from SSE at 24.0 m/s (86 km/h, 54 mph) Cloud Cover: 25%  
>           Humidity: 65% Pressure: 1021 mb (34 in) Location: Vila velha, Br Current Time: 2022-05-21 17:04:45  
>           Observation Time: 08:04 PM (America/Sao_Paulo) **Source: WeatherStack.com**  

A fonte 3 é o site WeatherStack.com. A fonte 4 apresentou problemas na minha máquina. Não consegui fazer uma consulta usando o parâmetro -W, ou seja, não consegui
pesquisar por uma cidade específica. Não consegui encontrar o motivo desse problema, mas teste em sua máquina, pode ser um problema localizado.

>leandro@leandro:~$ inxi  -w --weather-source 4  
>Weather:   Temperature: 21.9 C (71 F) Conditions: Clear Current Time: 2022-05-21 17:06:48 (America/Sao_Paulo)  
>           **Source: DarkSky.net**  
>leandro@leandro:~$ inxi  -w --weather-source 4  
>Weather:   Temperature: 21.9 C (71 F) Conditions: Clear Current Time: 2022-05-21 17:06:48 (America/Sao_Paulo)  
>           **Source: DarkSky.net**  

As fontes de pesquisa de 5 a 9 podem não estar disponível para consulta, mas, por sorte, a opção 5 estava disponível para mim no Ubuntu (verifique no seu sistema):

>leandro@leandro:~$ inxi  -w --weather-source 5  
>Weather:   Message: Error: invalid-source~;  
>Weather:   Temperature: 21.7 C (71 F) Conditions: clearsky_night Current Time: 2022-05-21 17:09:42 (America/Sao_Paulo)  
>           **Source: MET Norway**  
>  
>leandro@leandro:~$ inxi  -w --weather-source 6  
>Weather:   Message: Error: invalid-source~;  
>  
>leandro@leandro:~$ inxi  -w --weather-source 7  
>Weather:   Message: Error: invalid-source~;  
>  
>leandro@leandro:~$ inxi  -w --weather-source 8  
>Weather:   Message: Error: invalid-source~;  
>  
>leandro@leandro:~$ inxi  -w --weather-source 9  
>Weather:   Message: Error: invalid-source~;  

Como mencionei antes, mudar a fonte de dados é útil quando o limite de consultas para o serviço é atingido e esse limite é relativamente baixo na fonte padrão, então é
bom ter cuidado com o uso deste comando com scripts automatizados.

O manual informa que é possível fazer uma busca por latitude/longitude, porém em minha máquina essa pesquisa não é possível pois aparentemente há um bug. O
comando não permite consultar posições no hemisfério Sul(valor de latitude negativo). Por exemplo:

>leandro@leandro:~$ inxi -xxxW  -20.321021,-40.339347 --weather-source=1  
>Error 10: Unsupported value:  for option: W  
>Check -h for correct parameters.  

Contudo, eu consigo pesquisar por posições de latitude no hemisfério Norte(valor de latitude positivo). Por exemplo:

>leandro@leandro:~$ inxi -xxxW 20.321021,-40.339347 --weather-source=1
>Weather:   Temperature: 24.63 C (76 F) Conditions: overcast clouds 
>           Wind: from E at 5.4 m/s (20 km/h, 12 mph). Gusting to 5.4 m/s (20 km/h, 12 mph) Cloud Cover: 100% Humidity: 72% 
>           Pressure: 1020 mb (34 in) Location: 20.321021, -40.339347 Current Time: 2022-05-21 17:16:56 Observation Time: N/A 
>           Source: OpenWeatherMap.org


Pela mensagem, o erro parece estar no programa e não nas fontes de dados.

Caso queira testar com sua localização, use os seguintes passos para obtê-la via Google Maps:
* Abra o google maps em um navegador (https://www.google.com/maps)
* Use a caixa de pesquisa no lado esquerdo da tela para encontrar o endereço desejado
* Clique com o botão direito no ponto desejado
* Um pop-up será aberto na tela. O valor da latitude e da longitude vão aparecer no topo desse menu
* para copiar automaticamente esses valores para a área de transferência, clique com o botão esquerdo em cima dos valores.

![Benjamin Bannekat](/img/post/art01/pesquisa_coordenadas.png)

## Considerações Finais

Este é um método bastante útil e prático de obter dados meteorológicos pela linha de comando no Linux.

Eu fiz os testes usando uma máquina com Ubuntu Linux versão 20.04, mas essa ferramenta funciona e está disponível em todas as principais distribuições no mercado.

Um problema que notei ao fazer os testes foi que o horário da observação é variável entre as fontes de dados. Então pode haver uma variação enorme de uma fonte para outra.

Use esse recurso com parcimônia! Pesquisas automáticas podem ser bloqueadas e tentativas de contornar a restrição podem implicar no banimento permanente do serviço. 
Então muito cuidado ao criar scripts automatizados com ele!

