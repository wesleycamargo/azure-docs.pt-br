---
title: Ativos nos Serviços de Mídia – Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre a natureza dos ativos e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969568"
---
# <a name="assets"></a>Ativos

Nos Serviços de Mídia do Azure, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um Ativo, eles podem ser usados na análise de fluxos de trabalho de conteúdo, streaming e codificação dos Serviços de Mídia. Para obter mais informações, consulte [Carregar arquivos digitais na seção Ativos](#upload-digital-files-into-assets) abaixo.

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Microsoft Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blob de blocos nesse contêiner. Os Serviços de Mídia oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento esporádico ou para arquivar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). O armazenamento **de Arquivamento** é adequado para arquivar arquivos fontes quando não forem mais necessário (por exemplo, após terem sido decodificados).

A camada de armazenamento de **Arquivamento** só é recomendada para arquivos de origem muito grandes que já tenham sido codificados e a saída do trabalho de codificação foi colocada em um contêiner de blobs de saída. Os blobs no contêiner de saída que você deseja associar a um ativo e usar para transmitir ou analisar seu conteúdo, deve existir em uma camada de armazenamento de **frequente** ou **esporádico**.

## <a name="asset-definition"></a>Definição do ativo

A tabela a seguir mostra as propriedades do Ativo e retorna suas definições.

|NOME|DESCRIÇÃO|
|---|---|
|ID|ID de recurso totalmente qualificada para o recurso.|
|Nome|O nome do recurso.|
|properties.alternateId |A ID alternativa do Ativo.|
|properties.alternateId |A ID do ativo.|
|properties.container |O nome do contêiner de blob do ativo.|
|properties.created |A data de criação do ativo.<br/> Data e hora sempre estão no formato UTC.|
|properties.description|A descrição do ativo.|
|properties.lastModified |A data da última modificação do Ativo. <br/> Data e hora sempre estão no formato UTC.|
|properties.storageAccountName |O nome da conta de armazenamento.|
|properties.storageEncryptionFormat |O formato de criptografia do Ativo. Um de Nenhum ou MediaStorageEncryption.|
|Tipo|Tipo do recurso.|

Para uma definição completa, consulte [Ativos](https://docs.microsoft.com/rest/api/media/assets).

## <a name="upload-digital-files-into-assets"></a>Carregar os arquivos digitais em Ativos

Um dos fluxos de trabalho dos Serviços de Mídia do Azure comuns é carregar, codificar e transmitir um arquivo. Esta seção descreve as etapas gerais.

1. Use a API dos Serviços de Mídia do Azure v3 para criar um novo ativo de "entrada". Esta operação cria um contêiner na conta de armazenamento associada com sua conta de Serviços de Mídia do Azure. A API retorna o nome do contêiner (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Se você já tiver um contêiner de blob que deseja associar a um ativo, você pode especificar o nome do contêiner ao criar o ativo. Os Serviços de Mídia do Azure atualmente suportam apenas blobs na raiz do contêiner e não com caminhos no nome do arquivo. Portanto, um contêiner com o nome do arquivo "input.mp4" funcionará. Porém, um contêiner com o nome do arquivo "videos/inputs/input.mp4", não funcionará.

    Você pode usar a CLI do Azure para carregar diretamente em qualquer conta de armazenamento e contêiner que você tiver direitos na sua assinatura. <br/>O nome do contêiner deve ser exclusivo e seguir as diretrizes de nomeação de armazenamento. O nome não precisa seguir a formatação do nome do contêiner de ativo dos Serviços de Mídia do Azure (GUID do ativo). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obter uma URL de SAS com permissões de leitura / gravação que serão usadas para carregar arquivos digitais em contêiner do ativo. Você pode usar a API de Serviços de Mídia do Azure [listará as URLs de contêiner do ativo](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Use as SDKs ou as APIs de Armazenamento do Azure (por exemplo, a [API REST de Armazenamento](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [SDK do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para carregar arquivos no contêiner do Ativo. 
4. Use as APIs dos Serviços de Mídia v3 para criar uma transformação e um trabalho para processar seu ativo de "entrada". Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md).
5. Transmitir o conteúdo do ativo de "saída".

> [!TIP]
> Para obter um exemplo de .NET completo que mostra como: criar o ativo, obter uma URL SAS gravável para o contêiner do ativo no armazenamento, carregue o arquivo no contêiner no armazenamento usando a URL de SAS, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Criar um novo ativo

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo REST, consulte [Criar um ativo com exemplo de REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

O exemplo mostra como criar o **Corpo da Solicitação** onde você pode especificar informações úteis, como descrição, nome do contêiner, conta de armazenamento e outras informações.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Para obter um exemplo completo, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md). Nos Serviços de Mídia do Azure v3, entrada de um trabalho também pode ser criada de URLs HTTPS (consulte [criar uma entrada de trabalho de uma URL HTTPS](job-input-from-http-how-to.md)).

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
|Nome|Suporta: Eq, Gt, Lt|Suporta: Ordem crescente e decrescente|
|properties.alternateId |Suporta: Eq||
|properties.alternateId |Suporta: Eq||
|properties.container |||
|properties.created|Suporta: Eq, Gt, Lt| Suporta: Ordem crescente e decrescente|
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

Se Ativos são criados ou excluídos durante a paginação por meio da coleção, as alterações são refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

#### <a name="c-example"></a>Exemplo de C#

O exemplo de C# a seguir mostra como enumerar todos os ativos na conta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Exemplo REST

Considere o seguinte exemplo de onde $skiptoken é usado. Certifique-se de substituir *amstestaccount* pelo seu nome de conta e defina o valor *api-version* valor para a versão mais recente.

Se você solicitar uma lista de ativos como esta:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Você deve ver uma resposta semelhante a essa:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, você pode solicitar a próxima página, enviando uma solicitação get para:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
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

[Diferenças entre os Serviços de Mídia do Azure v2 e v3](migrate-from-v2-to-v3.md)
