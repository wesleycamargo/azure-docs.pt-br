---
title: Localizadores de Transmissão nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são os Localizadores de Transmissão e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 56e9a0b4eec347d2367c38ab00c6c9d9dca60752
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986877"
---
# <a name="streaming-locators"></a>Localizadores de Streaming

Para fornecer aos seus clientes um URL que eles podem usar para reproduzir arquivos de áudio ou vídeo codificados, você precisa criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) e criar os URLs de streaming. Para obter mais informações, consulte [Stream um arquivo](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Definição de StreamingLocator

A tabela a seguir mostra as propriedades do StreamingLocator e fornece suas definições.

|NOME|Tipo|DESCRIÇÃO|
|---|---|---|
|ID |string|ID de recurso totalmente qualificada para o recurso.|
|Nome   |string|O nome do recurso.|
|properties.alternativeMediaId  |string|ID de mídia alternativa deste localizador de streaming.|
|properties.assetName   |string|Nome do ativo|
|properties.contentKeys |StreamingLocatorContentKey[]|Os ContentKeys usadas por esse localizador de Streaming.|
|properties.created |string|O horário de criação do Localizador de Transmissão.|
|properties.defaultContentKeyPolicyName |string|Nome da ContentKeyPolicy padrão usada por este Streaming Locator.|
|properties.endTime |string|A hora final do Localizador de Transmissão.|
|properties.startTime   |string|A hora de início do Localizador de Transmissão.|
|properties.streamingLocatorId  |string|O StreamingLocatorId do Localizador de Transmissão.|
|properties.streamingPolicyName |string|Nome da política de fluxo usado por esse localizador de Streaming. Especifique o nome da política de Streaming que você criou ou use uma das políticas predefinidas de Streaming. As políticas predefinidas de Streaming disponíveis são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e ' Predefined_ MultiDrmStreaming'|
|Tipo   |string|Tipo do recurso.|

A definição completa, consulte [localizadores de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators).

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Os Serviços de Mídia do Microsoft Azure suportam as seguintes opções de consulta OData para ativos: 

* $filter 
* $orderby 
* $top 
* $skipToken 

Descrição do operador:

* Eq = igual a
* Ne = não é igual a
* Ge = maior que ou igual a
* Le = menor ou igual a
* Gt = maior que
* Lt = menor que

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades do StreamingLocator: 

|NOME|Filter|Classificar|
|---|---|---|
|ID |||
|Nome|Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq, ne, ge, le,  gt, lt|Em ordem crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|Tipo   |||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. É possível configurar o tamanho da página. 

Se StreamingLocators forem criados ou excluídos durante a paginação da coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

O seguinte exemplo C# mostra como enumerar todos os StreamingLocators na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [localizadores de Streaming - lista](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
