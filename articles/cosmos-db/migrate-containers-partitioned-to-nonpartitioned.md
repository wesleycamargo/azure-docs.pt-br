---
title: Migrar não particionada do Azure Cosmos DB contêineres para contêineres particionados
description: Saiba como migrar todos os contêineres existentes não particionada em contêineres particionados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e8aaee8cb7df794652b2c560c5250921c1a4e060
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160629"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrar os contêineres não particionado para contêineres particionados

O Azure Cosmos DB dá suporte à criação de contêineres sem uma chave de partição. No momento, você pode criar contêineres não particionada usando a CLI do Azure e Azure SDKs do Cosmos DB (.Net, Java, NodeJs) que têm uma versão inferior ou igual a 2. x. Você não pode criar contêineres não particionada usando o portal do Azure. No entanto, esses contêineres não particionada não são Elásticos e corrigiram a capacidade de armazenamento de 10 GB e taxa de transferência de limite de 10 K RU/s.

Os contêineres não particionadas são herdados e seus contêineres de não-particionada existentes devem ser migrados para contêineres particionados no armazenamento de escala e taxa de transferência. O Azure Cosmos DB fornece um mecanismo de sistema definidas para migrar seus contêineres não particionado para contêineres particionados. Este documento explica como todos os contêineres existentes não particionados serão migradas automaticamente em contêineres particionados. Você pode aproveitar o recurso de migração automática somente se você estiver usando a versão de V3 do SDKs em todos os idiomas.

> [!NOTE] 
> No momento, você não pode migrar as contas do MongoDB do Azure Cosmos DB e a API do Gremlin usando as etapas descritas neste documento. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrar o contêiner usando a chave de partição definida pelo sistema

Para dar suporte a migração, o Azure Cosmos DB define uma chave de partição definida pelo sistema chamada `/_partitionkey` em todos os contêineres que não têm uma chave de partição. Você não pode alterar a definição de chave de partição depois que os contêineres são migrados. Por exemplo, a definição de um contêiner que é migrado para um contêiner particionado será da seguinte maneira: 

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```
 
Depois que o contêiner é migrado, você pode criar documentos, preenchendo o `_partitionKey` propriedade juntamente com as outras propriedades do documento. O `_partitionKey` propriedade representa a chave de partição dos seus documentos. 

Escolher a chave de partição correta é importante utilizar a taxa de transferência provisionada de forma ideal. Para obter mais informações, consulte [como escolher uma chave de partição](partitioning-overview.md) artigo. 

> [!NOTE]
> Você pode tirar proveito da chave de partição definida do sistema somente se você estiver usando a versão mais recente/V3 dos SDKs em todos os idiomas.

O exemplo a seguir mostra um exemplo de código para criar um documento com a chave de partição definida do sistema e ler o documento:

**Representação JSON do documento**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Para o exemplo completo, consulte o [amostras do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repositório do GitHub. 
                      
## <a name="migrate-the-documents"></a>Migrar os documentos

Enquanto a definição de contêiner é aprimorada com uma propriedade de chave de partição, os documentos dentro do contêiner não são automaticamente migrados. Que significa que a propriedade de chave de partição do sistema `/_partitionKey` caminho não é adicionado automaticamente para os documentos existentes. Você precisará reparticionar os documentos existentes, lendo os documentos que foram criados sem uma chave de partição e reescrevê-los novamente com `_partitionKey` propriedade nos documentos. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Acessar os documentos que não têm uma chave de partição

Aplicativos podem acessar os documentos existentes que não têm uma chave de partição usando a propriedade de sistema especial chamada "CosmosContainerSettings.NonePartitionKeyValue", esse é o valor dos documentos não migrados. Você pode usar essa propriedade em todas as operações CRUD e consulta. O exemplo a seguir mostra um exemplo para ler um documento único do NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Para o exemplo completo sobre como fazer a repartição os documentos, consulte o [amostras do .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) repositório do GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilidade com os SDKs

Versão mais antiga do SDKs do Azure Cosmos DB como V2.x.x e V1.x.x não dão suporte a propriedade de chave de partição definidos pelo sistema. Assim, quando você ler a definição de contêiner de um SDK mais antigo, ele não contém nenhuma definição de chave de partição e esses contêineres se comportará exatamente como antes. Aplicativos que são criados com a versão mais antiga do SDKs continuam a funcionar com não particionada, sem quaisquer alterações. 

Se um contêiner migrado é consumido pela versão do SDK mais recente/V3 e começar a preencher a chave de partição definida pelo sistema em novos documentos, você não pode acessar (leitura, atualização, exclusão, consulta), documenta os SDKs mais antigos do mais.

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](account-overview.md)