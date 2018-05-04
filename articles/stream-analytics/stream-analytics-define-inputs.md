---
title: Transmitir dados como entrada no Azure Stream Analytics
description: Saiba mais sobre como configurar uma conexão de dados no Azure Stream Analytics. As entradas incluem um fluxo de dados de eventos e também dados de referência.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/19/2018
ms.openlocfilehash: 5ebf2d1025c8f9469a83a408cb79e3d944a601bc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Transmitir dados como entrada no Stream Analytics

O Stream Analytics aceita entrada de dados de vários tipos de fontes de eventos. A conexão de dados fornecida como uma entrada em um trabalho do Stream Analytics é conhecida como a *entrada* do trabalho. 

O Stream Analytics tem integração de primeira classe com fluxos de dados do Azure como entradas de três tipos de recursos:
- [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Esses recursos de entrada podem ser da mesma assinatura do Azure que o trabalho do Stream Analytics ou de uma assinatura diferente.

## <a name="compare-stream-and-reference-inputs"></a>Comparar as entradas de referência e de transmissão
Como os dados são enviados a uma fonte de dados, eles são consumidos pelo trabalho do Stream Analytics e processados em tempo real. As entradas são divididas em dois tipos: entradas de fluxo de dados e entradas de dados de referência.

### <a name="data-stream-input"></a>Entrada de fluxo de dados
Um fluxo de dados é uma sequência ilimitada de eventos ao longo do tempo. Os trabalhos do Stream Analytics devem conter pelo menos uma fonte de entrada de fluxo de dados. O Armazenamento de Blobs, os Hubs de Eventos e o Hub IoT têm suporte como fontes de entrada de fluxo de dados. Os Hubs de Eventos são usados para coletar fluxos de eventos de vários dispositivos e serviços. Esses fluxos podem incluir os feeds de atividades de mídia social, informações de mercado de ações ou dados de sensores. Os Hubs IoT são otimizados para coletar dados de dispositivos conectados em cenários da Internet das Coisas (IoT).  O Armazenamento de Blobs pode ser usado como uma fonte de entrada para ingerir dados em massa como uma transmissão, tais como arquivos de log.  

### <a name="reference-data-input"></a>Entrada de dados de referência
O Stream Analytics também dá suporte à entrada conhecida como *dados de referência*. Esses são dados auxiliares que são estáticos ou que são alterados lentamente. Normalmente, os dados de referência são usados para executar correlação e pesquisas. Por exemplo, você pode unir dados na entrada de fluxo de dados a dados nos dados de referência, assim como você realizaria uma junção SQL para pesquisar valores estáticos. O Armazenamento de Blob do Azure é a única fonte de entrada com suporte para dados de referência. Os blobs de fonte de dados de referência estão limitados a 100 MB de tamanho.

Para saber como criar entradas de dados de referência, veja [Usar dados de referência](stream-analytics-use-reference-data.md).  

### <a name="compression"></a>Compactação
O Stream Analytics oferece suporte à compactação em todas as fontes de entrada de fluxo de dados. Os tipos de referência para compactação com suporte no momento são: None, GZip e Deflate. O suporte para a compactação não está disponível para os dados de referência. Se o formato de entrada forem dados Avro compactados, eles serão manipulados de forma transparente. Você não precisa especificar o tipo de compactação com a serialização Avro. 

## <a name="create-or-edit-inputs"></a>Criar ou editar entradas
Para criar novas entradas e listar ou editar entradas existentes em seu trabalho de streaming, você pode usar o portal do Azure:
1. Abra o [portal do Azure](https://portal.azure.com) para localizar e selecionar o trabalho do Stream Analytics.
2. Selecione a opção de **Entradas** sob o título **CONFIGURAÇÕES**. 
4. A página **Entradas** lista as entradas existentes. 
5. Na página **Entradas**, selecione **Adicionar fluxo de entrada** ou **Adicionar entrada de referência** para abrir a página de detalhes.
6. Selecione uma entrada existente para editar os detalhes e selecione **Salvar** para editar uma entrada existente.
7. Selecione **Teste** na página de detalhes de entrada para verificar se as opções de conexão são válidas e estão funcionando. 
8. Clique com o botão direito do mouse no nome de uma entrada existente e selecione **Dados de entrada de exemplo** conforme necessário para outros testes.

Você também pode usar o [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [API REST](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-input), e [Visual Studio](stream-analytics-tools-for-visual-studio.md) para criar, editar e testar entradas de trabalho do Stream Analytics.

## <a name="stream-data-from-event-hubs"></a>Transmitir dados dos Hubs de Eventos

Os Hubs de Eventos do Azure fornecem ingestores de eventos altamente escalonável de publicação/assinatura. Um hub de eventos pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Os Hubs de Eventos e o Stream Analytics juntos fornecem uma solução de ponta a ponta para análise em tempo real. Os Hubs de Eventos permitem que você alimente eventos no Azure em tempo real e trabalhos do Stream Analytics podem processá-los em tempo real. Por exemplo, você pode enviar cliques da Web, leituras do sensor ou eventos de log online para Hubs de Eventos. Em seguida, você pode criar trabalhos do Stream Analytics para usar Hubs de Eventos como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

O carimbo de data/hora padrão de eventos provenientes dos Hubs de Eventos no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub de Eventos, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Grupos de consumidores
Cada entrada do trabalho do Hub de Eventos do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou ele tem várias entradas, algumas entradas podem ser lidas por mais de um leitor de downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite de Hub de Eventos dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics. Também há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter mais informações, confira o [Guia de programação de Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="stream-data-from-event-hubs"></a>Transmitir dados dos Hubs de Eventos
A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure para transmitir entrada de dados de um Hub de Eventos:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **Alias de entrada** |Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Assinatura** | Escolha a assinatura na qual existem os recursos de hub de Eventos. | 
| **Namespace do Hub de Eventos** | Um namespace Hub de Eventos é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também cria o namespace. |
| **Nome do Hub de Eventos** | O nome do Hub de Eventos para usar como entrada. |
| **Nome da política do Hub de Eventos** | A política de acesso compartilhado que fornece acesso ao Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub de Eventos manualmente.|
| **Grupo de consumidores de Hub de Eventos** (recomendado) | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. Esta cadeia de caracteres identifica o grupo de consumidores a ser usado para ingerir dados do hub de eventos. Se nenhum grupo de consumidores for especificado, o trabalho do Stream Analytics usará o grupo de consumidores $Default.  |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada. |
| **Codificação** | UTF-8 é o único formato de codificação com suporte no momento. |
| **Tipo de compactação do evento** | O tipo de compactação usado para ler o fluxo de dados de entrada, como None (padrão), GZip ou Deflate. |

Quando seus dados forem provenientes de uma entrada de fluxo de Hub de Eventos, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e a hora em que o evento foi recebido pelos Hubs de eventos. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ao usar o Hub de Eventos como um ponto de extremidade das Rotas do Hub IoT, é possível acessar os metadados do Hub IoT usando a [função GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Transmitir dados do Hub IoT
O Hub IoT do Azure é um ingestor de eventos altamente escalonável de publicação/assinatura e otimizado para cenários de IoT.

O carimbo de data/hora padrão de eventos provenientes de um Hub IoT no Stream Analytics é o carimbo de data/hora de que o evento foi recebido no Hub IoT, que é `EventEnqueuedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Apenas as mensagens enviadas com uma propriedade `DeviceClient` podem ser processadas.
> 

### <a name="consumer-groups"></a>Grupos de consumidores
Cada entrada do Hub IoT do Stream Analytics deve ser configurada para ter seu próprio grupo de consumidores. Quando um trabalho contém uma autojunção ou ele tem várias entradas, algumas entradas podem ser lidas por mais de um leitor de downstream. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite do Hub IoT do Azure dos cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho do Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um Hub IoT como uma entrada do fluxo de dados
A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure, quando você configura um Hub IoT como entrada de fluxo.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada.|
| **Assinatura** | Escolha a assinatura na qual existem os recursos de Hub IoT existentes. | 
| **Hub IoT** | O nome do Hub IoT para usar como entrada. |
| **Ponto de extremidade** | O ponto de extremidade para o Hub IoT.|
| **Nome da política de acesso compartilhado** | A política de acesso compartilhado que fornece acesso ao Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| **Chave da política de acesso compartilhado** | A chave de acesso compartilhado usada para autorizar o acesso ao Hub IoT.  Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub IoT manualmente. |
| **Grupo de consumidores** | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. O grupo de consumidores é usado para ingerir dados do Hub IoT. O Stream Analytics usa o grupo de consumidores $Default, a menos que você especifique o contrário.  |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) do fluxo de dados de entrada. |
| **Codificação** | UTF-8 é o único formato de codificação com suporte no momento. |
| **Tipo de compactação do evento** | O tipo de compactação usado para ler o fluxo de dados de entrada, como None (padrão), GZip ou Deflate. |


Ao usar dados de fluxo provenientes de um Hub IoT, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **EventProcessedUtcTime** | A data e a hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e a hora em que o evento foi recebido pelo Hub IoT. |
| **PartitionId** | A ID de partição com base em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Uma ID usada para correlacionar a comunicação bidirecional no Hub IoT. |
| **IoTHub.CorrelationId** | Uma ID usada em respostas a mensagens e comentários no Hub IoT. |
| **IoTHub.ConnectionDeviceId** | A ID de autenticação usada para enviar esta mensagem. Esse valor é marcado em mensagens servicebound pelo Hub IoT. |
| **IoTHub.ConnectionDeviceGenerationId** | A ID de geração do dispositivo autenticado que foi usado para enviar esta mensagem. Esse valor é marcado em mensagens servicebound pelo Hub IoT. |
| **IoTHub.EnqueuedTime** | A hora do recebimento da mensagem pelo Hub IoT. |
| **IoTHub.StreamId** | A propriedade de evento personalizado adicionada pelo dispositivo do remetente. |


## <a name="stream-data-from-blob-storage"></a>Transmitir dados do armazenamento de blobs
Para cenários com grandes quantidades de dados não estruturados para repositório na nuvem, o Armazenamento de Blobs do Azure oferece uma solução econômica e escalonável. Dados no Armazenamento de Blobs geralmente são considerados dados em repouso. No entanto, os dados de blobs podem ser processados como um fluxo de dados pelo Stream Analytics. 

O processamento de log é um cenário bastante usado para o uso de entradas de armazenamento de Blobs com o Stream Analytics. Nesse cenário, os arquivos de dados telemétricos foram capturados de um sistema e precisam ser analisados e processados para extrair dados significativos.

O carimbo de data/hora padrão de eventos de Armazenamento de Blobs no Stream Analytics é o carimbo de data/hora que o blob foi modificado pela última vez, que é `BlobLastModifiedUtcTime`. Para processar os dados como uma transmissão usando um carimbo de data/hora na carga do evento, você deve usar a palavra-chave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

As entradas formatadas em CSV *exigem* uma linha de cabeçalho para definir os campos do conjunto de dados, e todos os campos de linha de cabeçalho devem ser exclusivos.

No momento, o Stream Analytics não oferece suporte ao desserializar mensagens AVRO geradas pela captura de Hub de Eventos ou ponto de extremidade personalizado do Contêiner de Armazenamento do Azure Hub IoT.

> [!NOTE]
> O Stream Analytics não dá suporte para a adição de conteúdo a um arquivo de blob existente. O Stream Analytics exibirá cada arquivo apenas uma vez e quaisquer alterações que ocorram no arquivo após o trabalho ter lido os dados não serão processados. A prática recomendada é carregar todos os dados para um arquivo de blob de uma vez e, em seguida, adicionar outros eventos mais recentes em um arquivo diferente, o novo arquivo de blob.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurar o Armazenamento de Blobs como uma entrada de dados 

A tabela a seguir explica cada propriedade na página **Nova entrada** no portal do Azure, quando você configura o Armazenamento de Blobs como uma entrada de fluxo.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Assinatura** | Escolha a assinatura na qual existem os recursos de Hub IoT existentes. | 
| **Conta de armazenamento** | O nome da conta de armazenamento em que estão localizados os arquivos de blob. |
| **Chave de conta de armazenamento** | A chave secreta associada à conta de armazenamento. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Armazenamento de Blobs manualmente. |
| **Contêiner** | O contêiner para o blob de entrada. Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço de Armazenamento de Blobs do Azure, você deve especificar um contêiner para aquele blob. Você pode escolher **Usar contêiner existente** ou **Criar novo** para ter um novo contêiner criado.|
| **Padrão do caminho** (opcional) | O caminho do arquivo usado para localizar os blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das três variáveis a seguir: `{date}`, `{time}` ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O caractere `*` não é um valor permitido para o prefixo de caminho. Apenas <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blobs do Azure</a> válidos são permitidos. |
| **Formato de data** (opcional) | Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Se você usar a variável de hora no caminho, o formato de hora no qual os arquivos são organizados. Atualmente, o único valor com suporte é `HH` para horas. |
| **Formato de serialização do evento** | O formato de serialização (JSON, CSV ou Avro) para fluxos de dados de entrada. |
| **Codificação** | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| **Compactação** | O tipo de compactação usado para ler o fluxo de dados de entrada, como None (padrão), GZip ou Deflate. |

Quando seus dados forem provenientes de uma fonte de Armazenamento de Blobs, você poderá acessar alguns campos de metadados em sua consulta do Stream Analytics:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **BlobName** |O nome do blob de entrada de onde o evento veio. |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e hora da última modificação do blob. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Próximas etapas
Você aprendeu sobre as opções de conexão de dados no Azure para seus trabalhos do Stream Analytics. Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
