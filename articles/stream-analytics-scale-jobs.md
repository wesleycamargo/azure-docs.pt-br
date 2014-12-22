<properties title="Scale Azure Stream Analytics jobs" pageTitle="Escalonar trabalhos de Análise de fluxo | Azure" description="Learn how to scale Stream Analytics jobs" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

# Escalonar trabalhos de Análise de fluxo do Azure 

Aprenda a calcular *unidades de Streaming* para um trabalho de Análise de fluxo e como escalonar trabalhos de Análise de fluxo configurando partições de entrada, ajustando a definição de consulta e definindo unidades de fluxo de trabalho.

Uma definição de trabalho de Análise de fluxo do Azure inclui entradas, consulta e saída. As entradas são onde o trabalho lê os dados de fluxo de, a saída é onde o trabalho de enviar os resultados do trabalho e a consulta é usada para transformar o fluxo de entrada.  Um trabalho requer pelo menos uma fonte de entrada de fluxo de dados. A fonte de entrada de fluxo de dados pode ser um Hub de eventos do barramento de serviço do Azure ou um armazenamento de Blob do Azure. Para obter mais informações, consulte [Introdução à Análise de fluxo do Azure][stream.analytics.introduction], [Introdução à utilização da Análise de fluxo do Azure][stream.analytics.get.started] e [Guia do desenvolvedor da Análise de fluxo do Azure][stream.analytics.developer.guide]. 

O recurso disponível para processar trabalhos de análise de fluxo é medido por uma unidade de transmissão. Cada unidade de transmissão pode fornecer até a taxa de transferência de 1 MB por segundo. Cada trabalho requer um mínimo de uma unidade de transmissão, o que é o padrão para todos os trabalhos. Você pode definir até 12 unidades de streaming de um trabalho de análise de fluxo usando o portal de Gerenciamento do Azure. Cada assinatura do Azure pode ter até 12 unidades de streaming de todos os trabalhos em uma região específica. Para aumentar a unidades de streaming para sua assinatura até 100 unidades, contate o [Suporte da Microsoft][microsoft.support].

O número de unidades de streaming que um trabalho pode utilizar depende da configuração de partição de entradas e a consulta definida para o trabalho. O artigo mostra como calcular e ajustar a consulta para aumentar a taxa de transferência.

##Neste artigo
+ [Calcule o máximo de unidades de um trabalho de streaming](#calculate)
+ [Configurar a partição do trabalho da Análise de fluxo](#configure)
+ [Monitorar o desempenho do trabalho da Análise de fluxo](#monitor)
+ [Próximas etapas](#nextstep)


##<a name="calculate"></a>Calcule o máximo de unidades de um trabalho de streaming
O número total de unidades de streaming que pode ser usado por um trabalho de Análise de fluxo depende do número de etapas da consulta definida para o trabalho e o número de partições para cada etapa.

### Etapas de uma consulta
Uma consulta pode ter uma ou mais etapas. Cada etapa é uma subconsulta definida usando a palavra-chave WITH. A consulta somente fora da palavra-chave WITH também é contada como uma etapa. Por exemplo, a instrução SELECT na consulta a seguir:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

A consulta anterior tem duas etapas. 

> [WACOM.NOTE] Este exemplo de consulta será explicado posteriormente neste artigo.

### Uma etapa de partição

Particionamento de uma etapa exige as seguintes condições:

- A fonte de entrada deve ser particionada. Consulte o [guia do desenvolvedor da Análise de fluxo do Azure][stream.analytics.developer.guide] e o [guia do desenvolvedor de Hubs de evento do Azure][azure.event.hubs.developer.guide].
- A instrução SELECT da consulta deve ser lidos de uma origem de entrada particionada. 
- A consulta dentro da etapa deve ter a palavra-chave Partition By 

Quando uma consulta for particionada, os eventos de entrada será processado e agregados na partição separada grupos e saídas de eventos são gerados para cada um dos grupos. Se uma agregação combinada é desejável, crie uma segunda etapa não particionado para agregação.

A versão de visualização da Análise de fluxo do Azure não dá suporte a particionamento por nomes de coluna. Você pode particionar somente pelo campo PartitionId, que é um campo interno em sua consulta. O campo ParitionId indica de qual partição do fluxo de dados de origem do evento são.  Para obter detalhes, consulte [problemas conhecidos e limitações da Análise de fluxo do Azure][stream.analytics.limitations].

### Calcule o máximo de unidades de um trabalho de streaming

Todas as etapas não particionadas juntas podem escalar verticalmente até 6 unidades de streaming de um trabalho de Análise de fluxo. Para adicionar adicionais de streaming de unidades, uma etapa deve ser particionada. Cada partição pode ter 6 unidades de streaming.

<table border="1">
<tr><th>Consulta de um trabalho</th><th>Max unidades para o trabalho de streaming</th></td>

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
<li>A consulta contém duas etapas.</li>
<li>A instrução SELECT lê da entrada particionada.</li>
</ul>
</td>
<td>24 (18 etapa particionada) + 6 etapa não particionado</td></tr>
</table>

###Exemplo de escala
A consulta a seguir calcula o número de carros passando por uma estação de ligação com três pedágios dentro de uma janela de 3 minutos. Essa consulta pode ser dimensionada até 6 unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Para usar mais unidades de streaming para a consulta, tanto os dados de fluxo de entrada e a consulta deve ser particionada. Dada a partição do fluxo de dados definida como 3, a seguinte consulta modificada pode ser dimensionada até 18 unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Quando uma consulta for particionada, os eventos de entrada são processados e agregados na partição separada grupos e saídas de eventos são gerados para cada um dos grupos. O particionamento pode causar alguns resultados inesperados quando o campo Agrupar por não é a chave da partição na entrada de fluxo de dados. Por exemplo, o campo TollBoothId na consulta de exemplo anterior não é a Chave de partição de Input1. Os dados do pedágio #1 possam ser distribuídos em várias partições. Cada uma das partições Input1 será processada separadamente pela análise de fluxo e vários registros da contagem passagem carro do pedágio mesmo na mesma janela em cascata serão criados. Caso a chave de partição de entrada não pode ser alterada, esse problema pode ser corrigido corrigida pela adição de uma etapa adicional de partição não. Por exemplo:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Essa consulta pode ser dimensionada até 24 unidades de streaming. 

>[WACOM.NOTE] Se você estiver associando dois fluxos, certifique-se de que os fluxos são particionados por chave de partição da coluna que você faz as junções e têm o mesmo número de partições em ambos os fluxos.


##<a name="configure"></a>Configurar a partição do trabalho da Análise de fluxo

**Para ajustar a unidade de transmissão de um trabalho**

1. Entrar no [Portal de gerenciamento][azure.management.portal].
2. Clique em **Análise de fluxo** à esquerda.
3. Clique no trabalho de Análise de fluxo que deseja dimensionar.
4. Clique em **ESCALONAR** na parte superior da página.

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>Monitorar o desempenho do trabalho

Usando o portal de gerenciamento, você pode acompanhar a taxa de transferência de um trabalho de eventos por segundo:

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
Calcule a taxa de transferência esperada da carga de trabalho de eventos por segundo. Caso a taxa de transferência é menor do que o esperado, ajustar a partição de entrada, ajuste a consulta e adicionar unidades de streaming adicionais ao seu trabalho.

##<a name="nextstep"></a> Próximas etapas
Neste artigo, você aprendeu como calcular as unidades de streaming e como dimensionar um trabalho de Análise de fluxo. Para saber mais sobre análise de fluxo, consulte:

- [Introdução à Análise de fluxo do Azure][stream.analytics.introduction]
- [Começar a usar a Análise de fluxo do Azure][stream.analytics.get.started]
- [guia do desenvolvedor da Análise de fluxo do Azure][stream.analytics.developer.guide]
- [Problemas conhecidos e limitações da Análise de fluxo do Azure][Stream.analytics.limitations]
- [Referência de linguagem de consulta da Análise de fluxo do Azure][Stream.analytics.query.language.reference]
- [Referência de API REST de gerenciamento da Análise de fluxo do Azure][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/pt-br/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


