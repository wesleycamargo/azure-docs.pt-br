---
title: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
description: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 69eafaa3be7a966bec179713d3e7eecd48ea3b67
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243683"
---
# <a name="manage-database-account"></a>Gerenciar conta do banco de dados

Este artigo descreve como gerenciar sua conta do Cosmos DB para instalar hospedagem múltipla, adicionar ou remover uma região, configurar várias regiões de gravação e prioridades de failover. 

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurar clientes para hospedagem múltipla

### <a id="configure-clients-multi-homing-dotnet"></a>.NET

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Assíncrono

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Síncrono

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Adicionar ou remover regiões de sua conta de banco de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Navegue até sua conta do Azure Cosmos DB e abra o menu **Replicar dados globalmente**.

2. Para adicionar regiões, selecione uma ou mais regiões do mapa clicando nos hexágonos vazios com o rótulo **"+"** correspondente à região desejada. Você também pode adicionar uma região selecionando a opção **+ Adicionar região** e escolhendo uma região no menu suspenso.

3. Para remover regiões, desmarque uma ou mais regiões do mapa clicando em hexágonos azuis com uma marca de seleção ou selecionando o ícone de “Lixeira” (🗑) ao lado da região, no lado direito.

4. Clique em Salvar para salvar as alterações.

   ![Menu Adicionar/Remover regiões](./media/how-to-manage-database-account/add-region.png)

No modo de gravação de região única, é possível remover a região de gravação. Você deverá realizar o failover para uma região diferente antes de excluir a região de gravação atual.

No modo de gravação de várias regiões, você pode adicionar ou remover qualquer região, desde que exista pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Quando você cria uma conta de banco de dados, verifique se a opção **Gravação em Várias Regiões** está habilitada.

![Captura de tela de criação de conta do Cosmos DB](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modelo do Resource Manager

O código JSON a seguir é um modelo do Resource Manager de exemplo. Você pode usá-lo para implantar uma conta do Azure Cosmos DB com uma política de consistência como Desatualização Limitada, um intervalo de desatualização máxima de 5 segundos e o número máximo de solicitações de desatualização toleradas em 100. Para saber mais sobre o formato do modelo do Resource Manager e a sintaxe, confira a documentação do [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a name="enable-manual-failover-for-your-cosmos-account"></a>Habilitar failover manual para a conta do Cosmos

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Navegue até sua conta do Azure Cosmos DB e abra o menu **Replicar dados globalmente**.

2. Clique no botão **"Failover Manual"** na parte superior do menu.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **"Failover Manual"**, selecione a nova região de gravação e marque a caixa que indica o entendimento de que isso alterará sua região de gravação.

4. Clique em "OK" para disparar o failover.

   ![Menu do portal de failover manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a name="enable-automatic-failover-for-your-cosmos-account"></a>Habilitar failover automático a conta do Cosmos

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos DB, abra o painel **"Replicar dados globalmente"**. 

2. Clique no botão **"Failover Automático"** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **"Failover Automático"**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Clique em Salvar na parte inferior do menu.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

Você também pode definir as prioridades de failover nesse menu.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-cosmos-account"></a>Definir prioridades de failover para a conta do Cosmos

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos DB, abra o painel **"Replicar dados globalmente"**. 

2. Clique no botão **"Failover Automático"** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **"Failover Automático"**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Você pode modificar a prioridade de failover clicando e arrastando as regiões de leitura nos três pontos à esquerda da linha que aparecem quando o mouse é passado sobre ela. 

5. Clique em Salvar na parte inferior do menu.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

Não é possível modificar a região de gravação nesse menu. Você deve fazer um failover manual para alterar a região de gravação manualmente.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Cosmos DB Account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Próximas etapas

Você pode aprender a gerenciar conflitos de dados e níveis de consistência no Cosmos DB usando os seguintes documentos:

* [Como gerenciar consistência](how-to-manage-consistency.md)
* [Como gerenciar conflitos entre regiões](how-to-manage-conflicts.md)

