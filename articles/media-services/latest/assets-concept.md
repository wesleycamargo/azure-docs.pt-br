---
title: Ativos nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre a natureza dos ativos e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 61555eb6cca6995215ce43051abbda9aa43539ec
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284831"
---
# <a name="assets"></a>Ativos

Um **Ativo** contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um ativo, eles podem ser usados nos fluxos de trabalho de codificação e streaming dos serviços de mídia.

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blobs de blocos nesse contêiner. Você pode interagir com os arquivos de Ativo nos contêineres usando os clientes do SDK de Armazenamento.

Os Serviços de Mídia do Azure oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o armazenamento esporádico. O armazenamento frio é adequado para arquivar arquivos fontes quando não for mais necessário (por exemplo, após terem sido decodificados).

Nos Serviços de Mídia v3, a entrada do trabalho pode ser criada de ativos ou de URLs HTTP(s). Para criar um ativo que possa ser usado como entrada para o trabalho, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

Além disso, leia sobre [contas de armazenamento nos Serviços de Mídia](storage-account-concept.md) e [transformações e trabalhos](transform-concept.md).

## <a name="asset-definition"></a>Definição do ativo

A tabela a seguir mostra as propriedades do Ativo e retorna suas definições.

|NOME|type|DESCRIÇÃO|
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

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades do Ativo: 

|NOME|Filter|Classificar|
|---|---|---|
|ID|Suporta:<br/>É igual a<br/>Maior que<br/>Menor que|Suporta:<br/>Crescente<br/>Decrescente|
|Nome|||
|properties.alternateId |Suporta:<br/>É igual a||
|properties.alternateId |Suporta:<br/>É igual a||
|properties.container |||
|properties.created|Suporta:<br/>É igual a<br/>Maior que<br/>Menor que|Suporta:<br/>Crescente<br/>Decrescente|
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

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. 

Se uma resposta de consulta tiver muitos itens (atualmente mais de 1000), o serviço retorna uma propriedade "\@odata.nextLink" para a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. O tamanho da página não é configurável pelo usuário. 

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

> [!div class="nextstepaction"]
> [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
