---
title: Ativos nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre a natureza dos ativos e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 62cc4634a0f76b0562d5b3c1355a7442fc5cf989
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985226"
---
# <a name="assets"></a>Ativos

Um **Ativo** contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um ativo, eles podem ser usados nos fluxos de trabalho de codificação e streaming dos serviços de mídia.

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blobs de blocos nesse contêiner. Você pode interagir com os arquivos de Ativo nos contêineres usando os clientes do SDK de Armazenamento.

Os Serviços de Mídia do Azure oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o armazenamento esporádico. O armazenamento frio é adequado para arquivar arquivos fontes quando não for mais necessário (por exemplo, após terem sido decodificados).

Nos Serviços de Mídia v3, a entrada do trabalho pode ser criada de ativos ou de URLs HTTP(s). Para criar um ativo que possa ser usado como entrada para o trabalho, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

Além disso, leia sobre [contas de armazenamento nos Serviços de Mídia](storage-account-concept.md) e [transformações e trabalhos](transform-concept.md).

## <a name="asset-definition"></a>Definição do ativo

A tabela a seguir mostra as propriedades do Ativo e retorna suas definições.

|NOME|Tipo|DESCRIÇÃO|
|---|---|---|
|ID|string|ID de recurso totalmente qualificada para o recurso.|
|Nome|string|O nome do recurso.|
|properties.alternateId |string|A ID alternativa do Ativo.|
|properties.alternateId |string|A ID do ativo.|
|properties.container |string|O nome do contêiner de blob do ativo.|
|properties.created |string|A data de criação do ativo.|
|properties.description |string|A descrição do ativo.|
|properties.lastModified |string|A data da última modificação do Ativo.|
|properties.storageAccountName |string|O nome da conta de armazenamento.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |O formato de criptografia do Ativo. Um de Nenhum ou MediaStorageEncryption.|
|Tipo|string|Tipo do recurso.|

Para a definição completa, consulte [Ativos](https://docs.microsoft.com/rest/api/media/assets).

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

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades do Ativo: 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|Suportes: Eq, Gt, Lt|Suportes: em ordem crescente e decrescente|
|properties.alternateId |Suportes: Eq||
|properties.alternateId |Suportes: Eq||
|properties.container |||
|properties.created|Suportes: Eq, Gt, Lt| Suportes: em ordem crescente e decrescente|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|Tipo|||

O exemplo C# a seguir filtra a data da criação:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 1000.

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. É possível configurar o tamanho da página. 

Se Ativos são criados ou excluídos durante a paginação por meio da coleção, as alterações são refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada.) 

O exemplo de C# a seguir mostra como enumerar todos os ativos na conta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [Ativos - Lista](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia:

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v2|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos Serviços de Mídia|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do servidor oferecida pelo Armazenamento do Microsoft Azure, chave gerenciada pelo Azure ou pelo cliente|Com suporte|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do cliente oferecida pelo armazenamento do Azure, chave gerenciada pelo cliente no Key Vault|Sem suporte|Sem suporte|

<sup>1</sup> Enquanto os Serviços de Mídia deem suporte para tratamento de conteúdo sem qualquer forma de criptografia/limpo, não é recomendável fazer isso.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. Isso significa que o v3 funciona com recursos criptografados de armazenamento existentes, mas não permite a criação de novos recursos.

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
