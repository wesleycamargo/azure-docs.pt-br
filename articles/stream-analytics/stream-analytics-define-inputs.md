<properties
	pageTitle="Conexão de dados: entradas de fluxo de dados de um fluxo de eventos | Microsoft Azure"
	description="Saiba mais sobre como configurar uma conexão de dados com o Stream Analytics chamada de “entradas”. As entradas incluem um fluxo de dados de eventos e também dados de referência."
	keywords="fluxo de dados, conexão de dados, fluxo de eventos"
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
	ms.date="02/22/2016"
	ms.author="jeffstok"/>

# Conexão de dados: saiba mais sobre entradas de fluxo de dados de eventos para o Stream Analytics

A conexão de dados com o Stream Analytics é um fluxo de dados de eventos que partem de uma fonte de dados. Isso é chamado de "entrada". O Stream Analytics possui integração de primeiro nível com fontes de fluxo de dados do Azure como Hub de Eventos, Hub IoT e Armazenamento de Blobs que pode ocorrer da mesma assinatura, ou uma assinatura diferente, do Azure que seu trabalho de análise.

## Tipos de entrada de dados: fluxo de dados e dados de referência
Como os dados são enviados a uma fonte de dados, eles são consumidos pelo trabalho do Stream Analytics e processados em tempo real. As entradas são divididas em dois tipos distintos: entradas de fluxo de dados e entradas de dados de referência.

### Entradas de fluxo de dados
Um fluxo de dados é uma sequência ilimitada de eventos ao longo do tempo. Os trabalhos do Stream Analytics devem conter pelo menos uma fonte de entrada de fluxo de dados para ser consumida e transformada pelo trabalho. O Armazenamento de blob, os Hubs de Eventos e os Hubs IoT têm suporte como fontes de entrada de transmissão de dados. Os Hubs de Eventos são usados para coletar transmissões de eventos de vários dispositivos e serviços, como feeds de atividade de mídia social, informações sobre mercado de ações ou dados de sensores. Os Hubs IoT são otimizados para coletar dados de dispositivos conectados em cenários da Internet das Coisas (IoT). O Armazenamento de blob pode ser usado como uma fonte de entrada para ingerir dados em massa como uma transmissão.

### Dados de referência
O Stream Analytics dá suporte a um segundo tipo de fonte de entrada conhecido como dados de referência. São dados auxiliares que são estáticos ou que estão sofrendo uma alteração lenta ao longo do tempo e normalmente são usados para executar a correlação e pesquisas. O Armazenamento de Blob do Azure é a única fonte de entrada com suporte para dados de referência. Os blobs de fonte de dados de referência estão limitados a 100 MB de tamanho. Para saber como criar entradas de dados de referência, veja [Usar dados de referência](stream-analytics-use-reference-data.md)

## Criar uma entrada de fluxo de dados com um Hub de Eventos

Os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. É uma das entradas mais comumente usadas para Stream Analytics. Hubs de eventos e Stream Analytics juntos fornecem aos clientes uma solução de ponta a ponta para análise em tempo real. Hubs de eventos permitem que os clientes alimentem eventos no Azure em tempo real e trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, os clientes podem enviar cliques da Web, leituras de sensor, eventos de log online para Hubs de Eventos, e criar trabalhos do Stream Analytics que usem os Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

É importante observar que o carimbo de data/hora padrão de eventos provenientes dos Hubs de Eventos no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub de Eventos, que é EventEnqueuedUtcTime. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, use a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Grupos de consumidores

Cada entrada do trabalho do Hub de Eventos do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contiver uma autojunção ou várias entradas, algumas entradas poderão ser lidas por mais de um downstream de leitor, causando impacto no número de leitores de um único grupo de consumidores. Para evitar exceder o limite de Hub de Eventos dos 5 leitores por grupo de consumidores por partição, recomenda-se designar um grupo de consumidores para cada trabalho do Stream Analytics. Observe que também há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter detalhes, confira o [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).

## Configurar o Hub de Eventos como um fluxo de dados de entrada

A tabela a seguir explica cada propriedade na guia de entrada de do Hub de eventos com sua descrição:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
|------|------|
| Alias de entrada | Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada |
| Namespace do Barramento de Serviço | Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. |
| Hub de evento | O nome da sua entrada do Hub de Eventos. |
| Nome da política do Hub de Eventos. | A política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do Hub de eventos | A chave de Acesso Compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Grupo de consumidores de Hub de Eventos (Opcional) | O Grupo de Consumidores para ingerir dados do Hub de Eventos. Se não for especificado, os trabalhos do Stream Analytics usarão o Grupo de Consumidores Padrão para ingerir dados do Hub de Eventos. É recomendável usar um Grupo de consumidores distinto para cada trabalho do Stream Analytics. |
| Formato de serialização do evento | Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização (JSON, CSV ou Avro) você está usando para os fluxos de dados de entrada. |
| Codificação | UTF-8 é o único formato de codificação com suporte no momento. |

Quando seus dados forem provenientes de uma fonte de Hub de eventos, você pode acessar alguns campos de metadados em sua consulta do Stream Analytics. A tabela a seguir lista os campos e suas descrições.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| EventProcessedUtcTime | A data e a hora em que o evento foi processado pelo Stream Analytics. |
| EventEnqueuedUtcTime | A data e a hora em que o evento foi recebido pelos Hubs de eventos. |
| PartitionId | A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, você pode escrever uma consulta semelhante à seguinte:

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## Criar uma entrada de fluxo de dados do Hub IoT

O Hub IoT do Azure é um ingestor de eventos altamente escalonável de publicação/assinatura e otimizado para cenários de IoT. É importante observar que o carimbo de data/hora padrão de eventos provenientes dos Hubs IoT no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub IoT, que é EventEnqueuedUtcTime. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, use a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### Grupos de consumidores

Cada entrada do Hub IoT no Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contiver uma autojunção ou várias entradas, algumas entradas poderão ser lidas por mais de um downstream de leitor, causando impacto no número de leitores de um único grupo de consumidores. Para evitar exceder o limite do Hub IoT de cinco leitores por grupo de consumidores/partição, é recomendável designar um grupo de consumidores para cada trabalho do Stream Analytics.

## Configurar o Hub IoT como uma entrada de fluxo de dados

A tabela abaixo explica cada propriedade na guia de entrada do Hub IoT com sua descrição:

| NOME DA PROPRIEDADE | DESCRIÇÃO |
|------|------|
| Alias de entrada | Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada. |
| Hub IoT | Um Hub IoT é um contêiner para um conjunto de entidades de mensagens. |
| Ponto de extremidade | O nome do ponto de extremidade do Hub IoT. |
| Nome da Política de Acesso Compartilhado | A política de acesso compartilhado para conceder acesso ao Hub IoT. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave da Política de Acesso Compartilhado | A chave de Acesso Compartilhado usada para autenticar o acesso ao Hub IoT. |
| Grupo de Consumidores (Opcional) | O Grupo de Consumidores para ingerir dados do Hub IoT. Se não for especificado, os trabalhos do Stream Analytics usarão o Grupo de Consumidores Padrão para ingerir dados do Hub IoT. É recomendável usar um Grupo de consumidores distinto para cada trabalho do Stream Analytics. |
| Formato de serialização do evento | Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização (JSON, CSV ou Avro) você está usando para os fluxos de dados de entrada. |
| Codificação | UTF-8 é o único formato de codificação com suporte no momento. |

Quando seus dados forem provenientes de uma fonte do Hub IoT, é possível acessar alguns campos de metadados na consulta do Stream Analytics. A tabela a seguir lista os campos e suas descrições.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| EventProcessedUtcTime | A data e a hora em que o evento foi processado. |
| EventEnqueuedUtcTime | A data e a hora em que o evento foi recebido pelo Hub IoT. |
| PartitionId | A ID de partição com base em zero para o adaptador de entrada. |
| IoTHub.MessageId | Usado para correlacionar a comunicação bidirecional no Hub IoT. |
| IoTHub.CorrelationId | Usado em respostas a mensagens e comentários no Hub IoT. |
| IoTHub.ConnectionDeviceId | A ID autenticada usada para enviar esta mensagem, marcada nas mensagens associadas ao serviço pelo Hub IoT. |
| IoTHub.ConnectionDeviceGenerationId | A ID de geração do dispositivo autenticado usado para enviar esta mensagem, marcada nas mensagens associadas ao serviço pelo Hub IoT. |
| IoTHub.EnqueuedTime | Hora do recebimento da mensagem pelo Hub IoT. |
| IoTHub.StreamId | Propriedade de evento personalizado adicionada pelo dispositivo do remetente. |

## Criar uma entrada de fluxo de dados do Armazenamento de Blobs

Para cenários com grandes quantidades de dados não estruturados para armazenamento na nuvem, o Armazenamento de Blob oferece uma solução econômica e escalonável. Os dados no [Armazenamento de blob](https://azure.microsoft.com/services/storage/blobs/) são geralmente considerados dados “em repouso”, mas podem ser processados como uma transmissão de dados pelo Stream Analytics. Um cenário comum para entradas do armazenamento de Blob com o Stream Analytics é o processamento de log, no qual a telemetria é capturada de um sistema e precisa ser analisada e processada para extrair dados significativos.

É importante observar que o carimbo de data/hora padrão de eventos de armazenamento de Blob no Stream Analytics é o carimbo de data/hora em que o blob foi modificado pela última vez, que é *isBlobLastModifiedUtcTime*. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, use a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [AZURE.NOTE] O Stream Analytics não dá suporte para a adição de conteúdo a um blob existente. O Stream Analytics só exibirá um blob uma vez e quaisquer alterações feitas depois dessa leitura não serão processadas. A melhor prática é carregar todos os dados uma vez e não adicionar nenhum evento a mais ao repositório de blobs.

A tabela a seguir explica cada propriedade na guia de entrada do armazenamento de Blob com sua descrição:

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde estão localizados os arquivos de blob.</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contêiner de armazenamento
</td>
<td>Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [opcional]</td>
<td>O caminho do arquivo usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das três variáveis a seguir:<BR>{data}, {hora},<BR>{partição}<BR>Exemplo 1: cluster1/logs/{data}/{hora}/{partição}<BR>Exemplo 2: cluster1/logs/{data}<P>Observe que "*" não é um valor permitido para pathprefix. Apenas <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blobs do Azure</a> válidos são permitidos.</td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH.</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização (JSON, CSV ou Avro) você está usando para os fluxos de dados de entrada.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical.</td>
</tr>
</tbody>
</table>

Quando seus dados forem provenientes de uma fonte de armazenamento de Blob, você pode acessar alguns campos de metadados em sua consulta do Stream Analytics. A tabela a seguir lista os campos e suas descrições.

| PROPRIEDADE | DESCRIÇÃO |
|------|------|
| BlobName | O nome do blob de entrada de onde o evento veio. |
| EventProcessedUtcTime | A data e a hora em que o evento foi processado pelo Stream Analytics. |
| BlobLastModifiedUtcTime | A data e hora da última modificação do blob. |
| PartitionId | A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, você pode escrever uma consulta semelhante à seguinte:

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas
Você aprendeu sobre as opções de conexão de dados no Azure para seus trabalhos do Stream Analytics. Para saber mais sobre o Stream Analytics, confira:

- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0224_2016-->