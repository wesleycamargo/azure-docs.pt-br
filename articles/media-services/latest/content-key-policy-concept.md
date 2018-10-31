---
title: Diretivas de Chave de Conteúdo nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação de quais são as Políticas de Chave de Conteúdo e como elas são usadas pelos Serviços de Mídia do Azure.
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
ms.openlocfilehash: 9a5ef8df9b1ca87430fb5e8d1da94f1899c4a856
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985855"
---
# <a name="content-key-policies"></a>Políticas da Chave de Conteúdo

É possível usar os Serviços de Mídia do Azure para proteger sua mídia desde o momento que ela sai do seu computador até o armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (Gerenciamento de Direitos Digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.

No Azure Media Services v3, as Políticas de Chave de Conteúdo permitem que você especifique como a chave de conteúdo é entregue aos clientes finais por meio do componente Entrega de Chave dos Serviços de Mídia. Para obter mais informações, consulte [visão geral da proteção de conteúdo](content-protection-overview.md).

## <a name="contentkeypolicies-definition"></a>Definição de ContentKeyPolicies

A tabela a seguir mostra as propriedades do ContentKeyPolicy e fornece suas definições.

|NOME|Tipo|DESCRIÇÃO|
|---|---|---|
|ID|string|ID de recurso totalmente qualificada para o recurso.|
|Nome|string|O nome do recurso.|
|properties.created |string|A data de criação da política|
|properties.description |string|Uma descrição para a política.|
|properties.lastModified    |string|A data da última modificação da política|
|Properties.Options |ContentKeyPolicyOption[]|As principais opções de política.|
|properties.policyId    |string|A ID de política herdada.|
|Tipo   |string|Tipo do recurso.|

A definição completa, consulte [políticas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

O Media Services suporta as seguintes opções de consulta OData para ContentKeyPolicies: 

* $filter 
* $orderby 
* $top 
* $skipToken 

Descrição do operador:

* EQ = igual a
* Ne = não é igual a
* Ge = maior que ou igual a
* Le = menor ou igual a
* Gt = maior que
* Lt = menor que

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades StreamingPolicy: 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|properties.created |Eq, ne, ge, le,  gt, lt|Em ordem crescente e decrescente|
|properties.description |Eq, ne, ge, le, gt, lt||
|properties.lastModified    |Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|Properties.Options |||
|properties.policyId    |Eq, ne||
|Tipo   |||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. Você não pode configurar o tamanho da página. 

Se StreamingPolicy for criado ou excluído durante a paginação da coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

O seguinte exemplo C# mostra como enumerar todos os ContentKeyPolicies na conta.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Para exemplos de REST, consulte [Políticas de Chave de Conteúdo - Lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>Próximas etapas

[Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)

[Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
