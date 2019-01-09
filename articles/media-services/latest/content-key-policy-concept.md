---
title: Políticas de Chave de Conteúdo nos Serviços de Mídia – Azure | Microsoft Docs
description: Este artigo fornece uma explicação de quais são as Políticas de Chave de Conteúdo e como elas são usadas pelos Serviços de Mídia do Azure.
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
ms.custom: seodec18
ms.openlocfilehash: f12632b20d516c81e21a50cfdda7e40d4163afc1
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742211"
---
# <a name="content-key-policies"></a>Políticas da Chave de Conteúdo

É possível usar os Serviços de Mídia do Azure para proteger sua mídia desde o momento que ela sai do seu computador até o armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.

Nos Serviços de Mídia do Azure v3, uma [Política de Chave de Conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) permite que você especifique como a chave de conteúdo será entregue aos clientes finais por meio do componente Entrega de Chave dos Serviços de Mídia. Para obter mais informações, consulte [visão geral da proteção de conteúdo](content-protection-overview.md).

Recomenda-se reutilizar a mesma ContentKeyPolicy para todos os Ativos. ContentKeyPolicies são atualizáveis, portanto, se você quiser fazer uma rotação de chaves, poderá adicionar um novo ContentKeyPolicyOption à ContentKeyPolicy existente com uma restrição de token com as novas chaves. Ou você pode atualizar a chave de verificação principal e a lista de chaves de verificação alternativas na política e na opção existentes. Pode levar até 15 minutos para que os caches de entrega de Chaves atualizem e selecionem a política atualizada.

## <a name="contentkeypolicy-definition"></a>Definição ContentKeyPolicy

A tabela a seguir mostra as propriedades do ContentKeyPolicy e fornece suas definições.

|NOME|DESCRIÇÃO|
|---|---|
|ID|ID de recurso totalmente qualificada para o recurso.|
|Nome|O nome do recurso.|
|properties.created |A data de criação da política|
|properties.description |Uma descrição para a política.|
|properties.lastModified|A data da última modificação da política|
|Properties.Options |As principais opções de política.|
|properties.policyId|A ID de política herdada.|
|Tipo|Tipo do recurso.|

A definição completa, consulte [políticas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

O Media Services suporta as seguintes opções de consulta OData para ContentKeyPolicies: 

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

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades ContentKeyPolicies: 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|properties.created |Eq, ne, ge, le,  gt, lt|Em ordem crescente e decrescente|
|properties.description |Eq, ne, ge, le, gt, lt||
|properties.lastModified|Eq, ne, ge, le, gt, lt|Em ordem crescente e decrescente|
|Properties.Options |||
|properties.policyId|Eq, ne||
|Tipo|||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. É possível configurar o tamanho da página. 

Se ContentKeyPolicies forem criados ou excluídos durante a paginação da coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

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
