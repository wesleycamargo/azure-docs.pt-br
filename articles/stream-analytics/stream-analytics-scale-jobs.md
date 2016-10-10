<properties
	pageTitle="Dimensionar trabalhos do Stream Analytics para aumentar a produtividade | Microsoft Azure"
	description="Aprenda a dimensionar trabalhos do Stream Analytics configurando partições de entrada, ajustando a definição da consulta e definindo unidades de streaming de trabalho."
	keywords="streaming de dados, processamento de dados de streaming, ajuste de análise"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="09/26/2016"
	ms.author="jeffstok"/>

# Dimensionar trabalhos do Stream Analytics do Azure para aumentar a produtividade do processamento de dados do fluxo

Aprenda a ajustar trabalhos de análise e calcular *unidades de streaming* para o Stream Analytics e como dimensionar esses trabalhos configurando partições de entrada, ajustando a definição de consulta de análise e definindo unidades de streaming de trabalho.

## Quais são as partes de um trabalho do Stream Analytics?
Uma definição de trabalho de Stream Analytics inclui entradas, consulta e saída. As entradas são de onde o trabalho lê o fluxo de dados, a consulta é usada para transformar o fluxo de entrada e a saída é para onde são enviados os resultados do trabalho.

Um trabalho requer pelo menos uma fonte de entrada para streaming de dados. A fonte de entrada do fluxo de dados pode ser armazenada em um Hub de eventos do Barramento de Serviço do Azure ou um armazenamento de Blob do Azure. Para obter mais informações, consulte [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md) e [Começar a usar o Stream Analytics do Azure](stream-analytics-get-started.md).

## Configurando unidades streaming
SUs (Unidades de streaming) representam os recursos e a capacidade de computação necessária para executar um trabalho no Stream Analytics do Azure. As SUs fornecem uma maneira de descrever a capacidade de processamento de evento relativa, com base em uma medida combinada de CPU, memória e taxas de leitura e gravação. Cada unidade de streaming corresponde a aproximadamente 1MB/segundo de transferência.

A escolha de quantas SUs são necessárias para um trabalho específico depende da configuração de partição das entradas e da consulta definida para o trabalho. Você pode selecionar até sua cota em unidades de streaming para um trabalho, usando o Portal do Clássico Azure. Cada assinatura do Azure por padrão tem uma cota de até 50 unidades de streaming para todos os trabalhos de análise em uma região específica. Para aumentar a unidades de streaming para suas assinaturas, entre em contato com o [Suporte da Microsoft](http://support.microsoft.com).

O número de unidades de streaming que um trabalho pode utilizar depende da configuração de partição de entradas e da consulta definida para o trabalho. Observe também que um valor válido para as unidades de fluxo deve ser usado. Os valores válidos começam em 1, 3, 6 e, em seguida, para cima em incrementos de 6, conforme mostrado abaixo.

![Dimensionamento de unidade de fluxo do Stream Analytics do Azure][img.stream.analytics.streaming.units.scale]

O artigo mostra como calcular e ajustar a consulta para aumentar a taxa de transferência dos trabalhos de análise.

## Trabalho embaraçosamente paralelo
O trabalho embaraçosamente paralelo é o cenário mais escalonável que temos no Stream Analytics do Azure. Ele conecta uma partição da entrada para uma instância da consulta a uma partição da saída. Obter esse paralelismo exige alguns requisitos:

1.  Se sua lógica de consulta for dependente da mesma chave que está sendo processada pela mesma instância de consulta, você deverá garantir que os eventos sejam encaminhados para a mesma partição da entrada. No caso de Hubs de Eventos, isso significa que os Dados do Evento precisam ter a **PartitionKey** definida ou você pode usar remetentes particionados. Para Blob, isso significa que os eventos são enviados à mesma pasta de partição. Se sua lógica de consulta não exigir que a mesma chave seja processada pela mesma instância de consulta, você poderá ignorar esse requisito. Um exemplo disso seria uma consulta simples de seleção/projeto/filtro.
2.	Depois que os dados são dispostos como precisam ser no lado da saída, precisamos garantir que a consulta seja particionada. Isso exige que você use **Partition By** em todas as etapas. Várias etapas são permitidas, mas todas elas devem ser particionadas pela mesma chave. Outra coisa a ser observada é que, atualmente, a chave de particionamento precisa ser definida como **PartitionId** para ter um trabalho totalmente paralelo.
3.	Atualmente, somente Hubs de Eventos e Blobs permitem a saída particionada. Para a saída de Hubs de Eventos, você precisa configurar o campo **PartitionKey** para ser **PartitionId**. Para Blob, você não precisa fazer nada.
4.	Outra coisa a ser observada, o número de partições de entrada deve ser igual ao número de partições de saída. A saída de Blob não permite partições no momento, mas isso não é problema, pois ela herdará o esquema de particionamento da consulta upstream. Exemplos de valores de partição que permitiriam um trabalho totalmente paralelo:
	1.	8 partições de entrada de Hubs de Eventos e 8 partições de saída de Hubs de Eventos
	2.	8 partições de entrada de Hubs de Eventos e Saída de Blob
	3.	8 partições de saída de Blob e Saída de Blob
	4.	8 partições de entrada de Blob e 8 partições de saída de Hubs de Eventos

Veja aqui alguns cenários de exemplo que são embaraçosamente paralelos.

### Consulta simples
Entrada — Hubs de Eventos com 8 partições Saída — Hub de Eventos com 8 partições

**Consulta:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Essa consulta é um filtro simples e, como tal, não precisamos nos preocupar com o particionamento da entrada que enviamos aos Hubs de Eventos. Você perceberá que a consulta tem **Partition By** de **PartitionId**, portanto, atenderemos ao requisitos nº 2 acima. Para a saída, é necessário configurar a saída de Hubs de Eventos no trabalho para ter o campo **PartitionKey** definido como **PartitionId**. Uma última verificação, partições de entrada = = partições de saída. Essa topologia é embaraçosamente paralela.

### Consulta com chave de agrupamento
Entrada — Hubs de Eventos com 8 partições Saída - Blob

**Consulta:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Essa consulta tem uma chave de agrupamento e, como tal, a mesma chave precisa ser processada pela mesma instância de consulta. Isso significa que precisamos enviar nossos eventos aos Hubs de Eventos de maneira particionada. Qual chave é importante para nós? **PartitionId** é um conceito de lógica de trabalho, a chave real que nos interessa é **TollBoothId**. Isso significa que devemos definir a **PartitionKey** dos dados do evento que enviamos aos Hubs de Eventos para ser a **TollBoothId** do evento. A consulta tem **Partition By** de **PartitionId**, então, tudo certo. Para a saída, uma vez que é Blob, não precisamos nos preocupar com a configuração de **PartitionKey**. Para o requisito nº 4, mais uma vez, é Blob, então não precisamos nos preocupar com isso. Essa topologia é embaraçosamente paralela.

### Consulta de várias etapas com chave de agrupamento ###
Entrada — Hub de Eventos com 8 partições Saída — Hub de Eventos com 8 partições

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Essa consulta tem uma chave de agrupamento e, como tal, a mesma chave precisa ser processada pela mesma instância de consulta. Podemos usar a mesma estratégia que da consulta anterior. A consulta tem várias etapas. Cada etapa tem **Partition By** de **PartitionId**? Sim, então está tudo certo. Para a saída, precisamos definir a **PartitionKey** como **PartitionId**, como discutido acima, e também podemos ver que ela tem o mesmo número de partições que a entrada. Essa topologia é embaraçosamente paralela.


## Cenários de exemplo que NÃO são embaraçosamente paralelos

### Contagem de partições incompatível ###
Entrada — Hubs de Eventos com 8 partições Saída — Hub de Eventos com 32 partições

Nesse caso, não importa do que se trata a consulta, pois a contagem de partições de entrada != contagem de partições de saída.

### Não usar Hubs de Eventos ou Blobs como saída
Entrada — Hubs de Eventos com 8 partições Saída — PowerBI

Atualmente, a saída do PowerBI não permite particionamento.

### Consulta de várias etapas com diferente valores de Partition By
Entrada — Hub de Eventos com 8 partições Saída — Hub de Eventos com 8 partições

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Como você pode ver, a segunda etapa usa **TollBoothId** como a chave de particionamento. Isso não se iguala à primeira etapa e, portanto, exigirá que façamos um embaralhamento.

Esses são alguns exemplos e contraexemplos de trabalhos do Stream Analytics que poderão atingir uma topologia embaraçosamente paralela e, com ela, o potencial para escala máxima. Para trabalhos que não se encaixam em um desses perfis, futuramente serão feitas atualizações detalhando como escalar ao máximo alguns dos outros cenários canônicos do Stream Analytics.

Por enquanto, use as orientações gerais abaixo:

## Calcule o máximo de unidades de um trabalho de streaming
O número total de unidades de streaming que pode ser usado por um trabalho de Análise de fluxo depende do número de etapas da consulta definida para o trabalho e o número de partições para cada etapa.

### Etapas de uma consulta
Uma consulta pode ter uma ou mais etapas. Cada etapa é uma subconsulta definida usando a palavra-chave **WITH**. A única consulta que está fora da palavra-chave **WITH** também é contada como uma etapa. Por exemplo, a instrução **SELECT** na consulta a seguir:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId

A consulta anterior tem duas etapas.

> [AZURE.NOTE] Este exemplo de consulta será explicado posteriormente neste artigo.

### Uma etapa de partição

Particionamento de uma etapa exige as seguintes condições:

- A fonte de entrada deve ser particionada. Para obter mais informações, confira o [Guia de programação de Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).
- A instrução **SELECT** da consulta deve ser lida de uma origem de entrada particionada.
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

### Exemplos de escala
A consulta a seguir calcula o número de carros passando por uma estação de ligação com três pedágios dentro de uma janela de três minutos. Essa consulta pode ser ampliada para até seis unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar mais unidades de streaming para a consulta, tanto os dados de fluxo de entrada e a consulta devem ser particionadas. Dada a partição do fluxo de dados definida como 3, a seguinte consulta modificada pode ser dimensionada para até 18 unidades de streaming:

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partições separados. Eventos de saída também são gerados para cada um dos grupos. O particionamento pode causar alguns resultados inesperados quando o campo **Group-by** não é a Chave da partição na entrada de fluxo de dados. Por exemplo, o campo **TollBoothId** na consulta de exemplo anterior não é a Chave de Partição de Input1. Os dados do Pedágio #1 podem ser distribuídos em várias partições.

Cada uma das partições de Input1 será processada separadamente pela Análise de fluxo e vários registros da contagem de passagem de carros do mesmo pedágio na mesma janela em cascata serão criados. Se a chave de partição de entrada não puder ser alterada, esse problema poderá ser corrigido pela adição de uma etapa adicional sem partição, por exemplo:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Essa consulta pode ser dimensionada até 24 unidades de streaming.

>[AZURE.NOTE] Se você estiver unindo dois fluxos, certifique-se de eles sejam particionados por chave de partição da coluna em que você faz as junções e existe o mesmo número de partições em ambos os fluxos.


## Configurar a partição do trabalho da Análise de fluxo

**Para ajustar a unidade de streaming de um trabalho**

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Análise de fluxo** no painel à esquerda.
3. Clique no trabalho de Análise de fluxo que deseja dimensionar.
4. Clique em **ESCALA** na parte superior da página.

![Dimensionamento de unidade de fluxo do Stream Analytics do Azure][img.stream.analytics.streaming.units.scale]

No Portal do Azure, as configurações de Escala podem ser acessadas em Configurações:

![Configuração de trabalho do Stream Analytics no Portal do Azure][img.stream.analytics.preview.portal.settings.scale]

## Monitorar o desempenho do trabalho

Usando o portal de gerenciamento, você pode acompanhar a taxa de transferência de um trabalho de eventos por segundo:

![Análise de fluxo do Azure, monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho de eventos por segundo. Caso a taxa de transferência seja menor do que o esperado, ajuste a partição de entrada e a consulta e adicione unidades de streaming ao seu trabalho.

## Produtividade do Stream Analytics em escala — cenário Raspberry Pi


Para entender como os trabalhos do Stream Analytics são escalados em um cenário típico em termos de produtividade de processamento em várias Unidades de Streaming, este é um experimento que envia dados de sensor (clientes) ao Hub de Eventos, processa-os e envia alertas ou estatísticas como uma saída para outro Hub de Eventos.

O cliente está enviando os dados de sensor sintetizados aos Hubs de Eventos no formato JSON para Stream Analytics e a saída de dados também está no formato JSON. Os dados de exemplo seriam semelhantes a estes –

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Consulta: "Enviar um alerta quando a luz é desligada"

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Medindo a produtividade: a produtividade nesse contexto é a quantidade de dados de entrada processados pelo Stream Analytics em um período fixo (10 minutos). Para obter a melhor taxa de transferência de processamento para os dados de entrada, tanto a entrada do fluxo de dados quanto a consulta devem ser particionadas. Além disso, **COUNT()** é incluído na consulta para medir a quantidade de eventos de entrada processados. Para garantir que o trabalho não esteja apenas esperando pelos eventos de entrada, cada partição do Hub de Eventos de Entrada foi pré-carregado com dados de entrada suficientes (cerca de 300 MB).

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
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)


## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0928_2016-->