---
title: Políticas de streaming nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são as políticas de streaming e como são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 4d02ddf50660cd700b2f1c5999ceadfb472b6906
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380446"
---
# <a name="streaming-policies"></a>Políticas de Streaming

Em Serviços de Mídia do Azure v3, as Políticas de Streaming permitem definir protocolos de streaming e opções de criptografia para seus StreamingLocators. Você pode especificar o nome da política de Streaming que você criou ou usar uma das políticas predefinidas de Streaming. As políticas predefinidas de Streaming disponíveis atualmente são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e ' Predefined_ MultiDrmStreaming'.

> [!IMPORTANT]
> Ao usar a [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-los para o seu StreamingLocators, sempre que a mesmo opção de criptografia e protocolos sejam necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deve criar um novo StreamingPolicy para cada StreamingLocator.

## <a name="streamingpolicy-definition"></a>Definição de StreamingPolicy

A tabela a seguir mostra as propriedades de StreamingPolicy e fornece sus definições.

|NOME|Tipo|DESCRIÇÃO|
|---|---|---|
|ID|string|ID de recurso totalmente qualificada para o recurso.|
|Nome|string|O nome do recurso.|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs|Configuração de CommonEncryptionCbcs|
|properties.commonEncryptionCenc|CommonEncryptionCenc|Configuração de CommonEncryptionCenc|
|properties.created |string|Hora de criação da Política de Streaming|
|properties.defaultContentKeyPolicyName |string|ContentKey padrão usado pela política atual de Streaming|
|properties.envelopeEncryption  |EnvelopeEncryption|Configuração de EnvelopeEncryption|
|properties.noEncryption|NoEncryption|Configuração de NoEncryption|
|Tipo|string|Tipo do recurso.|

Para a definição completa, consulte [Políticas de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Os Serviços de Mídia são compatíveis com as seguintes opções de consulta OData para Políticas de Streaming: 

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

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades do StreamingPolicy: 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |Eq, ne, ge, le,  gt, lt|Em ordem crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|Tipo|||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. É possível configurar o tamanho da página. 

Se StreamingPolicy forem criados ou excluídos durante a paginação da coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

O seguinte exemplo C# mostra como enumerar todos os StreamingPolicies na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [Políticas de Streaming - Lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/streamingpolicies_list)

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
