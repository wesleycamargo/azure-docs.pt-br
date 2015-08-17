<properties 
	pageTitle="Aplicativo do Aprendizado de Máquina: Serviço de Detecção de Anomalias | Microsoft Azure" 
	description="A API de detecção de anomalias é um exemplo criado com o Aprendizado de Máquina do Microsoft Azure que detecta anomalias nos dados de série temporal com valores numéricos que são espaçados uniformemente no tempo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/28/2015" 
	ms.author="luisca"/>


# Serviço de Detecção de Anomalias do Aprendizado de Máquina#

##Visão geral

A API de detecção de anomalias é um exemplo criado com o Aprendizado de Máquina do Azure que detecta anomalias nos dados de série temporal com valores numéricos que são espaçados uniformemente no tempo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Esse serviço de detecção de anomalias pode detectar os seguintes tipos diferentes de anomalias em dados de série temporal:

1. Tendências positivas e negativas: ao monitorar o uso de memória em computação, por exemplo, uma tendência de aumento é indicativa de um vazamento de memória.

2. Aumentar o intervalo dinâmico de valores: por exemplo, ao monitorar as exceções geradas por um serviço, qualquer aumentos de intervalo dinâmico dos valores pode indicar instabilidade na integridade do serviço, e

3. Picos e quedas: por exemplo, ao monitorar o número de falhas de logon em um serviço ou o número de check-outs em um site de comércio eletrônico, picos ou quedas podem indicar um comportamento anormal.


Esses detectores rastreiam alterações em valores ao longo do tempo e relatam mudanças contínuas em seus valores. Eles não precisam de ajuste de limite ad hoc e suas pontuações podem ser usadas para controlar a taxa de falsos positivos. A API de detecção de anomalias é útil em vários cenários, como o monitoramento de serviço ao controlar KPIs ao longo do tempo, métricas de uso como o número de pesquisas, número de cliques, contadores de desempenho como memória, cpu, leituras de arquivos, etc. ao longo do tempo.

##Definição da API

O serviço fornece uma API baseada em REST sobre HTTPS que pode ser consumida de maneiras diferentes, incluindo um aplicativo Web ou móvel, R, Python, Excel etc. Temos um [aplicativo Web do Azure](http://anomalydetection-aml.azurewebsites.net/) que ajuda a executar a detecção de anomalias de serviço Web em seus dados e visualizar os resultados.

Você também pode enviar seus dados de série temporal para esse serviço por meio de uma chamada à API REST e será executada uma combinação dos três tipos de anomalias descritos acima. O serviço é executado na plataforma de Aprendizado de Máquina AzureML, escalado continuamente conforme suas necessidades de negócio e fornecendo SLAs de 99,95%.

A figura a seguir mostra um exemplo de anomalias detectadas em uma série temporal usando a estrutura acima. A série temporal tem duas alterações distintas de nível e três picos. Os pontos vermelhos mostram a hora em que a alteração de nível for detectada, enquanto as setas para cima vermelhas mostram os picos detectados.


![][1]

##Entrada

A API utiliza dois parâmetros de entrada

1. "data" representa a série temporal de entrada no formato: t1=v1;t2=v2;... 

 
	Dados de exemplo:
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "params" definido para "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3”, que controla a sensibilidade dos detectores de picos. Valores mais altos capturam picos mais elevados e vice-versa.

	URL de amostra com os parâmetros de entrada mencionados acima:

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###Saída###

A API executa esses detectores em seus dados de série temporal e retorna as pontuações de anomalias em cada ponto no tempo. Essa saída pode ser analisada usando um analisador simples, conforme mostrado em [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/). Ele fornece código de exemplo que mostra como conectar-se à API e analisar a saída. Os alertas gerados podem ser visualizados em um painel e/ou passados para especialistas humanos que podem executar ações corretivas.

Exemplo de saída para a entrada de exemplo acima:

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

que é uma representação da tabela a seguir:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Hora</th>
   <th align="left" valign="middle">Dados</th>
   <th align="left" valign="middle">Tspike</th>
   <th align="left" valign="middle">Zspike</th>
   <th align="left" valign="middle">Valores martingale</th>
   <th align="left" valign="middle">Indicador de alerta</th>
   <th align="left" valign="middle">Valores martingale (2)</th>
   <th align="left" valign="middle">Indicador de alerta (2)</th>
   </tr>
<tr>
   <td valign="middle">21/9/2014 11:05</td>
   <td valign="middle">3</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-0,687952591</td>
   <td valign="middle">0</td>
   <td valign="middle">-0,687952591</td>
   <td valign="middle">0</td>
   </tr>
<tr>
<td valign="middle">21/9/2014 11:10</td>
   <td valign="middle">9,09</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1,070304977</td>
   <td valign="middle">0</td>
   <td valign="middle">-0,884548154</td>
   <td valign="middle">0</td>
    </tr>
<tr>
<td valign="middle">21/9/2014 11:15</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1,051862374</td>
   <td valign="middle">0</td>
   <td valign="middle">-1,1738002814</td>
   <td valign="middle">0</td>
   </tr>
   </table>

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=August15_HO6-->