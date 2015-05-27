<properties
	pageTitle="Escalonar trabalhos de Análise de fluxo | Azure"
	description="Saiba como dimensionar trabalhos de análise de fluxo"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="04/28/2015"
	ms.author="jeffstok"/>

# Escalonar trabalhos de Análise de fluxo do Azure

Aprenda a calcular *Unidades de Streaming* para um trabalho de Análise de fluxo e como escalonar trabalhos de Análise de fluxo configurando partições de entrada, ajustando a definição de consulta e definindo unidades de fluxo de trabalho.

Uma definição de trabalho de Análise de fluxo do Azure inclui entradas, consulta e saída. As entradas são de onde o trabalho lê o fluxo de dados, a consulta é usada para transformar o fluxo de entrada e a saída é para onde o trabalho envia os resultados do trabalho.

Um trabalho requer pelo menos uma fonte de entrada de fluxo de dados. A fonte de entrada do fluxo de dados pode ser armazenada em um Hub de eventos do Barramento de Serviço do Azure ou um armazenamento de Blob do Azure. Para saber mais, consulte [Introdução à análise de fluxo do Azure](stream-analytics-introduction.md), [Introdução ao uso da Análise de fluxo do Azure](stream-analytics-get-started.md) e [Guia do desenvolvedor da Análise de fluxo do Azure](stream-analytics-developer-guide.md).

O recurso disponível para processar trabalhos de análise de fluxo é medido por uma unidade de transmissão. Cada unidade de transmissão pode fornecer até a taxa de transferência de 1 MB por segundo. Cada trabalho requer um mínimo de uma unidade de transmissão, o que é o padrão para todos os trabalhos. Você pode definir até 50 unidades de streaming para um trabalho de Análise de fluxo usando o Portal do Azure. Cada assinatura do Azure pode ter até 50 unidades de streaming para todos os trabalhos em uma região específica. Para aumentar a unidades de streaming para sua assinatura (até 100 unidades), contate o [Suporte da Microsoft](http://support.microsoft.com).

O número de unidades de streaming que um trabalho pode utilizar depende da configuração de partição de entradas e da consulta definida para o trabalho. O artigo mostra como calcular e ajustar a consulta para aumentar a taxa de transferência.


## Calcule o máximo de unidades de um trabalho de streaming
O número total de unidades de streaming que pode ser usado por um trabalho de Análise de fluxo depende do número de etapas da consulta definida para o trabalho e o número de partições para cada etapa.

### Etapas de uma consulta
Uma consulta pode ter uma ou mais etapas. Cada etapa é uma subconsulta definida usando a palavra-chave WITH. A única consulta que está fora da palavra-chave WITH também é contada como uma etapa. Por exemplo, a instrução SELECT na consulta a seguir:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId, PartitionId

A consulta anterior tem duas etapas.

> [AZURE.NOTE]Este exemplo de consulta será explicado posteriormente neste artigo.

### Uma etapa de partição

Particionamento de uma etapa exige as seguintes condições:

- A fonte de entrada deve ser particionada. Para saber mais, consulte [Guia do desenvolvedor de Análise de fluxo do Azure](stream-analytics-developer-guide.md) e [Guia de programação de hubs de evento](azure-event-hubs-developer-guide.md).
- A instrução SELECT da consulta deve ser lidos de uma origem de entrada particionada.
- A consulta dentro da etapa deve ter a palavra-chave **Partition By**

Quando uma consulta for particionada, os eventos de entrada será processado e agregados na partição separada grupos e saídas de eventos são gerados para cada um dos grupos. Se uma agregação combinada é desejável, crie uma segunda etapa não particionado para agregação.

### Calcule o máximo de unidades de um trabalho de streaming

Todas as etapas não particionadas juntas podem escalar verticalmente até seis unidades de streaming de um trabalho de Análise de fluxo. Para adicionar adicionais de streaming de unidades, uma etapa deve ser particionada. Cada partição pode ter seis unidades de streaming.

<table border="1">
<tr><th>Consulta de um trabalho</th><th>Máximo de unidades de streaming para o trabalho</th></td>

<tr><td>
<ul>
<li>A consulta contém uma única etapa.</li>
<li>A etapa não está particionada.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>O fluxo de dados de entrada é particionado por 3.</li>
<li>A consulta contém uma única etapa.</li>
<li>A etapa é particionada.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>A consulta contém duas etapas.</li>
<li>Nenhuma das etapas é particionada.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>A entrada fluxo de dados é particionada por 3.</li>
<li>A consulta contém duas etapas. A etapa de entrada é particionada e a segunda etapa não é.</li>
<li>A instrução SELECT lê da entrada particionada.</li>
</ul>
</td>
<td>24 (18 para as etapas particionadas + 6 para as etapas não particionadas)</td></tr>
</table>

### Exemplo de escala
A consulta a seguir calcula o número de carros passando por uma estação de ligação com três pedágios dentro de uma janela de três minutos. Essa consulta pode ser ampliada para até seis unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar mais unidades de streaming para a consulta, tanto os dados de fluxo de entrada e a consulta devem ser particionadas. Dada a partição do fluxo de dados definida como 3, a seguinte consulta modificada pode ser dimensionada para até 18 unidades de streaming:

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partições separados. Eventos de saída também são gerados para cada um dos grupos. O particionamento pode causar alguns resultados inesperados quando o campo **Group-by** não é a Chave da partição na entrada de fluxo de dados. Por exemplo, o campo TollBoothId na consulta de exemplo anterior não é a Chave de partição de Input1. Os dados do Pedágio #1 podem ser distribuídos em várias partições.

Cada uma das partições de Input1 será processada separadamente pela Análise de fluxo e vários registros da contagem de passagem de carros do mesmo pedágio na mesma janela em cascata serão criados. Se a chave de partição de entrada não puder ser alterada, esse problema poderá ser corrigido pela adição de uma etapa adicional sem partição, por exemplo:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, ParititonId

Essa consulta pode ser dimensionada até 24 unidades de streaming.

>[AZURE.NOTE]Se você estiver associando dois fluxos, certifique-se de que os fluxos são particionados por chave de partição da coluna que você faz as junções e têm o mesmo número de partições em ambos os fluxos.


## Configurar a partição do trabalho da Análise de fluxo

**Para ajustar a unidade de streaming de um trabalho**

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Análise de fluxo** no painel à esquerda.
3. Clique no trabalho de Análise de fluxo que deseja dimensionar.
4. Clique em **ESCALA** na parte superior da página.

![Análise de fluxo do Azure, configurar a escala do trabalho][img.stream.analytics.configure.scale]


## Monitorar o desempenho do trabalho

Usando o portal de gerenciamento, você pode acompanhar a taxa de transferência de um trabalho de eventos por segundo:

![Análise de fluxo do Azure, monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho de eventos por segundo. Caso a taxa de transferência seja menor do que o esperado, ajuste a partição de entrada e a consulta e adicione unidades de streaming ao seu trabalho.

##Taxa de transferência ASA em escala - cenário no Raspberry Pi


Para entender como o ASA é dimensionado em um cenário típico em termos da taxa de transferência de processamento em várias unidades de streaming, este é um experimento em que dados de sensor (clientes) são enviados para o Hub de eventos, o ASA os processa e envia alertas ou estatísticas como uma saída para outro Hub de eventos.

O cliente está enviando os dados de sensor sintetizados para o Hub de eventos no formato JSON para ASA e saída de dados também está no formato JSON. Os dados de exemplo seriam semelhantes a estes –

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Consulta: "Enviar um alerta quando a luz é desligada"

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Medindo a taxa de transferência: a taxa de transferência nesse contexto é a quantidade de dados de entrada processados pelo ASA em um determinado período de tempo (10 minutos). Para obter a melhor taxa de transferência de processamento para os dados de entrada, tanto a entrada do fluxo de dados quanto a consulta devem ser particionadas. Além disso, COUNT() é incluído na consulta para medir a quantidade de eventos de entrada processados. Para garantir que o ASA não esteja apenas esperando pelos eventos de entrada, cada partição do Hub de eventos de entrada foi pré-carregado com dados de entrada suficientes (cerca de 300 MB).

Abaixo estão os resultados com o aumento do número de unidades de streaming e as contagens de partições correspondentes nos Hubs de eventos.

<table border="1">
<tr><th>Partições de entrada</th><th>Partições de saída</th><th>Unidades de streaming</th><th>Taxa de transferência mantida
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 MB/s</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)


## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.limitations]: stream-analytics-limitations.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54-->