---
title: Dimensionar trabalhos do Stream Analytics para aumentar a produtividade | Microsoft Docs
description: "Aprenda a dimensionar trabalhos do Stream Analytics configurando partições de entrada, ajustando a definição da consulta e definindo unidades de streaming de trabalho."
keywords: "streaming de dados, processamento de dados de streaming, ajuste de análise"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9a2b16fc6dff687e2a1fa03c9194d50711f53476
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017


---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Dimensionar trabalhos do Stream Analytics do Azure para aumentar a produtividade do processamento de dados do fluxo
Este artigo explica como você pode ajustar a consulta do Stream Analytics para aumentar a produtividade para os trabalhos do Streaming Analytics. Aprenda a dimensionar trabalhos do Stream Analytics configurando partições de entrada, ajustando a definição da consulta de analíticos calculando e configurando trabalho de *unidades de streaming* (SUs). 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quais são as partes de um trabalho do Stream Analytics?
Uma definição de trabalho de Stream Analytics inclui entradas, consulta e saída. As entradas são de onde o trabalho lê o fluxo de dados. A consulta é usada para transformar o fluxo de entrada de dados e a saída é para onde são enviados os resultados do trabalho.  

Um trabalho requer pelo menos uma fonte de entrada para streaming de dados. A fonte de entrada do fluxo de dados pode ser armazenada em um Hub de eventos do Barramento de Serviço do Azure ou um armazenamento de Blobs do Azure. Para saber mais, veja [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md) e [Começar a usar o Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-event-hubs-and-azure-storage"></a>Partições em Hubs de evento ou armazenamento do Azure
Dimensionamento de um trabalho do Stream Analytics tira proveito de partições na entrada ou saída. Particionamento permite que você divida dados em subconjuntos com base em uma chave de partição. Um processo que consome os dados (como um trabalho de Streaming Analytics) pode consumir e gravar diferentes partições em paralelo, o que aumenta a taxa de transferência. Quando você trabalha com Streaming Analytics, você pode tirar proveito do particionamento em Hubs de eventos e em armazenamento de Blobs. 

Para obter mais informações sobre partições, consulte os seguintes artigos:

* [Visão geral dos recursos de Hubs de Eventos](../event-hubs/event-hubs-features.md#partitions)
* [Particionamento de dados](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="streaming-units-sus"></a>Unidades de streaming (SUs)
Unidades de streaming (SUs) representam os recursos e a capacidade de computação necessária para executar um trabalho no Stream Analytics do Azure. As SUs fornecem uma maneira de descrever a capacidade de processamento de evento relativa, com base em uma medida combinada de CPU, memória e taxas de leitura e gravação. Cada SU corresponde a aproximadamente 1MB/segundo de transferência. 

A escolha de quantas SUs são necessárias para um trabalho específico depende da configuração de partição das entradas e da consulta definida para o trabalho. Você pode selecionar até sua cota de SUs para um trabalho. Cada assinatura do Azure, por padrão, tem uma cota de até 50 SUs para todos os trabalhos analíticos em uma região específica. Para aumentar as SUs para suas assinaturas, entre em contato com o [Suporte da Microsoft](http://support.microsoft.com). Os valores válidos para o SUs por trabalho são 1, 3, 6 e em incrementos de 6.

## <a name="embarrassingly-parallel-jobs"></a>Trabalhos embaraçosamente paralelos
Um trabalho *embaraçosamente paralelo* é o cenário mais escalonável que temos no Stream Analytics do Azure. Ele conecta uma partição da entrada para uma instância da consulta a uma partição da saída. Este paralelismo tem os seguintes requisitos:

1. Se sua lógica de consulta for dependente da mesma chave que está sendo processada pela mesma instância de consulta, você deverá garantir que os eventos sejam encaminhados para a mesma partição da entrada. Para Hubs de eventos, isso significa que os dados de evento devem ter o conjunto de valor **PartitionKey**. Como alternativa, você pode usar os remetentes particionados. Para armazenamento de Blobs, isso significa que os eventos são enviados à mesma pasta de partição. Se sua lógica de consulta não exigir que a mesma chave seja processada pela mesma instância de consulta, você pode ignorar esse requisito. Um exemplo disso seria uma consulta simples de seleção/projeto/filtro.  

2. Depois que os dados são dispostos como precisam ser no lado da saída, você precisa garantir que a consulta seja particionada. Isso exige que você use **Partition By** em todas as etapas. Várias etapas são permitidas, mas todas elas devem ser particionadas pela mesma chave. Atualmente, a chave de particionamento deve ser definida como **PartitionId** para que o trabalho seja totalmente paralelo.  

3. Atualmente, somente Hubs de eventos e armazenamento de Blobs permitem a saída particionada. Para a saída do Hub de eventos, você deve configurar a chave de partição para ser **PartitionId**. Para armazenamento de Blobs de saída, você não precisa fazer nada.  

4. O número de partições de entrada deve ser igual ao número de partições de saída. Atualmente, o armazenamento de Blobs de saída não suporta partições. Mas isso é ok, porque ele herda o esquema de particionamento da consulta de upstream. Exemplos de valores de partição que permitem um trabalho totalmente paralelo:  

   * 8 partições de entrada de Hubs de Eventos e 8 partições de saída de Hubs de Eventos
   * 8 partições de entrada de Hubs de Eventos e Saída de armazenamento de Blobs  
   * 8 partições de entrada de armazenamento de Blobs e Saída de armazenamento de Blobs  
   * 8 partições de entrada de armazenamento de Blobs e 8 partições de saída de Hubs de Eventos  

As seções a seguir discutem alguns cenários de exemplo que são embaraçosamente paralelos.

### <a name="simple-query"></a>Consulta simples

* Entrada: Hub de eventos com 8 partições
* Entrada: Hub de eventos com 8 partições

Consulta:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Essa consulta é um filtro simples. Portanto, nós não precisamos se preocupar sobre particionamento da entrada que está sendo enviada para o hub de eventos. Observe que a consulta inclui **Partition By PartitionId**, portanto, ela preenche o requisito #2 anterior. Para a saída, é necessário configurar a saída de Hubs de Eventos no trabalho para ter a chave de partição definida como **PartitionId**. Uma última verificação é certificar-se de que o número de partições de entrada é igual ao número de partições de saída.

### <a name="query-with-a-grouping-key"></a>Consulta com chave de agrupamento

* Entrada: Hub de eventos com 8 partições
* Saída: Armazenamento de Blobs

Consulta:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tem uma chave de agrupamento. Portanto, a mesma chave deve ser processada pela mesma instância de consulta, que significa que eventos devem ser enviados para os Hubs de eventos de modo particionado. Mas qual chave deve ser usada? **PartitionId** é um conceito de lógica de trabalho. A chave com a qual nós nos importamos, na verdade, é **TollBoothId**, portanto, o valor **PartitionKey** dos dados do evento deve ser **TollBoothId**. Podemos fazer isso na consulta definindo **Partition By** para **PartitionId**. Como a saída é o armazenamento de Blobs, não precisamos nos preocupar sobre como configurar um valor de chave de partição, de acordo com o requisito #4.

### <a name="multi-step-query-with-a-grouping-key"></a>Consulta de várias etapas com chave de agrupamento
* Entrada: Hub de eventos com 8 partições
* Saída: Instância de Hub de eventos com 8 partições

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Essa consulta tem uma chave de agrupamento, portanto a mesma chave precisa ser processada pela mesma instância de consulta. Podemos usar a mesma estratégia como no exemplo anterior. Neste caso, a consulta tem várias etapas. Cada etapa tem **Partition By PartitionId**? Sim, para consulta preencher o requisito #3. Para a saída, é preciso definir a chave de partição para **PartitionId**, conforme discutido anteriormente. Também é possível observar que ela tem o mesmo número de partições como a entrada.

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Cenários de exemplo que *não* são embaraçosamente paralelos

Na seção anterior, mostramos alguns cenários embaraçosamente paralelos. Nesta seção, vamos discutir os cenários que não atendem a todos os requisitos para serem embaraçosamente paralelos. 

### <a name="mismatched-partition-count"></a>Contagem de partições incompatível
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 32 partições

Nesse caso, não importa qual é a consulta. Se a contagem de partição de entrada não corresponder à contagem de partição de saída, a topologia não é embaraçosamente paralela.

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>Não usando Hubs de Eventos ou armazenamento de Blobs como saída
* Entrada: Hub de eventos com 8 partições
* Saída: PowerBI

Atualmente, a saída do PowerBI não suporta particionamento. Portanto, esse cenário não é embaraçosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de várias etapas com diferentes valores por partição
* Entrada: Hub de eventos com 8 partições
* Saída: Hub de eventos com 8 partições

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Como você pode ver, a segunda etapa usa **TollBoothId** como a chave de particionamento. Esta etapa não é igual à primeira etapa e, portanto, exige que façamos um embaralhamento. 

Os exemplos anteriores mostram alguns trabalhos do Stream Analytics que está de acordo com (ou não) em uma topologia em paralela. Se eles estão em conformidade, eles têm o potencial para expansão máxima. Para trabalhos que não se encaixam em nenhum desses perfis, as diretrizes de expansão estarão disponíveis em atualizações futuras. Por enquanto, use as diretrizes gerais nas seções a seguir.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcule o máximo de unidades de um trabalho de streaming
O número total de unidades de streaming que pode ser usado por um trabalho de Análise de fluxo depende do número de etapas da consulta definida para o trabalho e o número de partições para cada etapa.

### <a name="steps-in-a-query"></a>Etapas de uma consulta
Uma consulta pode ter uma ou mais etapas. Cada etapa é uma subconsulta definida usando a palavra-chave **WITH**. A única consulta que está fora da palavra-chave **WITH** também é contada como uma etapa. Por exemplo, a instrução **SELECT** na consulta a seguir:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Esta consulta tem duas etapas.

> [!NOTE]
> Esta consulta é discutida em mais detalhes mais adiante neste artigo.
>  

### <a name="partition-a-step"></a>Uma etapa de partição
Particionamento de uma etapa exige as seguintes condições:

* A fonte de entrada deve ser particionada. 
* A instrução **SELECT** da consulta deve ser lida de uma origem de entrada particionada.
* A consulta dentro da etapa deve ter a palavra-chave **Partition By**.

Quando uma consulta for particionada, os eventos de entrada serão processados e agregados em diferentes grupos de partição e saídas de eventos são geradas para cada um dos grupos. Se você quiser uma agregação combinada, crie uma segunda etapa não particionada para agregação.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcule o máximo de unidades de um trabalho de streaming
Todas as etapas não particionadas juntas podem escalar verticalmente até seis unidades de Streaming (SUs) para um trabalho de Stream Analytics. Para adicionar SUs, uma etapa deve ser particionada. Cada partição pode ter seis unidades de streaming.

<table border="1">
<tr><th>Consultar</th><th>Máxima quantidade de SUs para o trabalho</th></td>

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
<li>O fluxo de dados de entrada é particionado por 3.</li>
<li>A consulta contém duas etapas. A etapa de entrada é particionada e a segunda etapa não é.</li>
<li>A instrução <strong>SELECT</strong> lê da entrada particionada.</li>
</ul>
</td>
<td>24 (18 para as etapas particionadas + 6 para as etapas não particionadas)</td></tr>
</table>

### <a name="examples-of-scaling"></a>Exemplos de escala

A consulta a seguir calcula o número de carros passando por um pedágio que tem três pedágios dentro de uma janela de três minutos. Essa consulta pode ser escalada verticalmente para até seis unidades de streaming.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar mais unidades de streaming para a consulta, tanto o fluxo de dados de entrada quanto a consulta devem ser particionados. Dada a partição do fluxo de dados definida como 3, a seguinte consulta modificada pode ser escalada verticalmente para até 18 unidades de streaming:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando uma consulta é particionada, os eventos de entrada são processados e agregados em grupos de partições separados. Eventos de saída também são gerados para cada um dos grupos. O particionamento pode causar alguns resultados inesperados quando o campo **GROUP BY** não for a chave da partição no fluxo de dados de entrada. Por exemplo, o campo **TollBoothId** na consulta de exemplo anterior não é a chave de partição de **Input1**. O resultado é que os dados do Pedágio #1 podem ser distribuídos em várias partições.

Cada uma das partições **Entrada1** serão processadas separadamente pelo Stream Analytics. Como resultado, vários registros da contagem de carros para o mesmo pedágio na mesma janela em cascata serão criados. Se a chave de partição de entrada não puder ser alterada, esse problema poderá ser corrigido pela adição de uma etapa adicional sem partição, por exemplo:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta pode ser escalada verticalmente para até 24 SUs.

> [!NOTE]
> Se você estiver unindo dois fluxos, certifique-se de eles sejam particionados por chave de partição da coluna que você usa para criar as junções. Além disso, certifique-se de que você tem o mesmo número de partições em ambos os fluxos.
> 
> 

## <a name="configure-stream-analytics-streaming-units"></a>Configurar as unidades de streaming do Stream Analytics

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na lista de recursos, localize o trabalho do Stream Analytics que você deseja escalar e abra-o.
3. Na folha de trabalho, em **Configurar**, clique em **Escala**.

    ![Configuração de trabalho do Stream Analytics no Portal do Azure][img.stream.analytics.preview.portal.settings.scale]

4. Use o controle deslizante para definir o SUs para o trabalho. Observe que você está limitado a configurações específicas de SU.


## <a name="monitor-job-performance"></a>Monitorar o desempenho do trabalho
Usando o portal do Azure, você pode acompanhar a taxa de transferência de um trabalho:

![Análise de fluxo do Azure, monitorar trabalhos][img.stream.analytics.monitor.job]

Calcule a taxa de transferência esperada da carga de trabalho. Caso a taxa de transferência seja menor do que o esperado, ajuste a partição de entrada e a consulta e adicione unidades de streaming ao seu trabalho.


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>Visualize a taxa de transferência do Stream Analytics em escala — cenário Raspberry Pi
Para ajudá-lo a entender como os trabalhos do Stream Analytics escalam, realizamos uma experiência com base na entrada de um dispositivo Raspberry Pi. Esse teste nos permitiu ver o efeito na taxa de transferência de várias partições e unidades de streaming.

Nesse cenário, o dispositivo envia dados de sensor (clientes) para um hub de eventos. Streaming Analytics processa os dados e envia um alerta ou estatísticas como uma saída para outro hub de eventos. 

O cliente envia dados de sensor em formato JSON. A saída de dados também está no formato JSON. Os dados tem esta aparência:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

A consulta a seguir é usada para enviar um alerta quando uma luz é desligada:

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Medida de taxa de transferência

Neste contexto, a produtividade é a quantidade de dados de entrada processados pelo Stream Analytics em um período fixo. (É medido por 10 minutos.) Para obter a melhor taxa de transferência de processamento para os dados de entrada, tanto a entrada do fluxo de dados quanto a consulta foram particionadas. Nós incluímos **COUNT()** na consulta para medir a quantidade de eventos de entrada processados. Para garantir que o trabalho não esteja apenas esperando pelos eventos de entrada, cada partição do Hub de Eventos de Entrada foi pré-carregada com dados de entrada de cerca de 300 MB.

A tabela a seguir mostra os resultados que vimos quando aumentamos o número de unidades de streaming e a partição correspondente conta nos Hubs de eventos.  

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

E o gráfico a seguir mostra uma visualização da relação entre SUs e taxa de transferência.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


