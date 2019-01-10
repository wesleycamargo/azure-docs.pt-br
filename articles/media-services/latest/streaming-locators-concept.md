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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 658843fd5acbe0d4e29947e99c00edf4909fe9f4
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742738"
---
# <a name="streaming-locators"></a>Localizadores de Streaming

É necessário fornecer aos clientes uma URL que eles possam usar para reproduzir arquivos de vídeo ou áudio codificados e, para isso, você precisa criar um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e criar as URLs de streaming. Para obter mais informações, consulte [Stream um arquivo](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Definição de StreamingLocator

A tabela a seguir mostra as propriedades do StreamingLocator e fornece suas definições.

|NOME|DESCRIÇÃO|
|---|---|
|ID |ID de recurso totalmente qualificada para o recurso.|
|Nome|O nome do recurso.|
|properties.alternativeMediaId|ID de mídia alternativa deste localizador de streaming.|
|properties.assetName|Nome do ativo|
|properties.contentKeys|Os ContentKeys usadas por esse localizador de Streaming.|
|properties.created|O horário de criação do Localizador de Transmissão.|
|properties.defaultContentKeyPolicyName|Nome da ContentKeyPolicy padrão usada por este Streaming Locator.|
|properties.endTime|A hora final do Localizador de Transmissão.|
|properties.startTime|A hora de início do Localizador de Transmissão.|
|properties.streamingLocatorId|O StreamingLocatorId do Localizador de Transmissão.|
|properties.streamingPolicyName |Nome da política de fluxo usado por esse localizador de Streaming. Especifique o nome da política de Streaming que você criou ou use uma das políticas predefinidas de Streaming. As Políticas de Streaming predefinidas disponíveis são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'|
|Tipo|Tipo do recurso.|

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
