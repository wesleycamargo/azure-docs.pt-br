---
title: Gerenciar políticas de indexação no Azure Cosmos DB
description: Aprenda a gerenciar políticas de indexação no Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 76275420e1e6ed7fdec8309da9e11a272f08fee0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005581"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerenciar políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, os dados são indexados seguindo [políticas de indexação](index-policy.md) que são definidas para cada contêiner. A política de indexação padrão para contêineres recém-criados impõe os índices de intervalo para qualquer cadeia de caracteres ou número, bem como os índices espaciais para qualquer objeto GeoJSON do tipo Point. Essa política pode ser substituída:

- do portal do Azure
- usando a CLI do Azure
- usando um dos SDKs

Uma [atualização de política de indexação](index-policy.md#modifying-the-indexing-policy) dispara uma transformação de índice. O andamento dessa transformação também pode ser acompanhado dos SDKs.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Contêineres do Azure Cosmos armazenam sua política de indexação como um documento JSON que o portal do Azure lhe permite editar diretamente.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Crie uma nova conta do Cosmos ou selecione uma conta existente.

1. Abra o painel do **Data Explorer** e selecione o contêiner no qual deseja trabalhar.

1. Clique em **Escala e Configurações**.

1. Modifique o documento JSON de política indexação (veja exemplos [abaixo](#indexing-policy-examples))

1. Clique em **Salvar** quando terminar.

![Gerenciar a indexação usando o portal do Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

O comando [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) da CLI do Azure permite que você substitua a definição de JSON da política de indexação de um contêiner:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Usar o SDK do .NET V2

O objeto `DocumentCollection` do [SDK do .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (veja [este Início Rápido](create-sql-api-dotnet.md) em relação a seu uso) expõe uma propriedade `IndexingPolicy` que permite que você altere o `IndexingMode` e adicione ou remova `IncludedPaths` e `ExcludedPaths`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Para acompanhar o progresso de transformação de índice, passe um objeto `RequestOptions` que define a propriedade `PopulateQuotaInfo` para `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

O objeto `DocumentCollection` do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (confira [este Início Rápido](create-sql-api-java.md) sobre seu uso) expõe os métodos `getIndexingPolicy()` e `setIndexingPolicy()`. O objeto `IndexingPolicy` que eles manipulam permite que você altere o modo de indexação e adicione ou remova caminhos incluídos ou excluídos.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

Para acompanhar o progresso da transformação do índice em um contêiner, passe um objeto `RequestOptions` que solicita as informações de cota a serem populadas, depois recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Usar o SDK do Node.js

A interface `ContainerDefinition` do [SDK do Node.js](https://www.npmjs.com/package/@azure/cosmos) (veja [este Início Rápido](create-sql-api-nodejs.md) quanto ao seu uso) expõe uma propriedade `indexingPolicy` que permite que você altere o `indexingMode` e adicione ou remova `includedPaths` e `excludedPaths`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Para acompanhar o progresso da transformação do índice em um contêiner, passe um objeto `RequestOptions` que define a propriedade `populateQuotaInfo` para `true` e depois recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Usar o SDK do Python

Ao usar o [SDK do Python](https://pypi.org/project/azure-cosmos/) (consulte [neste Início Rápido](create-sql-api-python.md) quanto ao seu uso), a configuração do contêiner é gerenciada como um dicionário. Desse dicionário, é possível acessar a política de indexação e todos os seus atributos.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Aqui estão alguns exemplos de políticas de indexação mostrados no formato JSON, que é como eles são expostos no portal do Azure. Os mesmos parâmetros podem ser definidos por meio da CLI do Azure ou qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Política de recusa para excluir seletivamente alguns caminhos de propriedade

    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Política de aceitação para incluir seletivamente alguns caminhos de propriedade

    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }

Observação: Geralmente, é recomendável usar uma política de indexação de **recusa** para permitir que o Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Como usar um índice espacial em apenas um caminho de propriedade específico

    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Como excluir todos os caminhos de propriedade, mas manter a indexação ativa

Essa política pode ser usada em situações em que o [recurso TTL (Time-to-Live)](time-to-live.md) está ativo, mas nenhum índice secundário é necessário (para usar o Azure Cosmos DB como um repositório chave-valor puro).

    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }

### <a name="no-indexing"></a>Nenhuma indexação

    {
        "indexingPolicy": "none"
    }

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

- [Visão geral de indexação](index-overview.md)
- [Política de indexação](index-policy.md)
