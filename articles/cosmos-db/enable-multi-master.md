---
title: Habilitar vários mestres para contas do Azure Cosmos DB
description: Este artigo descreve como habilitar o suporte de vários mestres ao criar uma conta do Azure Cosmos DB com o portal do Azure, o PowerShell, a CLI ou um modelo do Azure Resource Manager.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 71579e28f389d91498ef5f37c5d43dc9d5140234
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963212"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Habilitar vários mestres para contas do Azure Cosmos DB

O suporte a vários mestres é habilitado durante a criação de uma conta do Azure Cosmos DB. Uma conta do Azure Cosmos DB pode ser criada usando o portal do Azure, o PowerShell, a CLI ou um modelo do Azure Resource Manager.

> [!IMPORTANT]
> Atualmente, o suporte a vários mestres pode ser habilitado apenas para novas contas do Azure Cosmos DB. As contas existentes do Azure Cosmos DB não podem usar o recurso. Estamos trabalhando para fornecer suporte para contas existentes e anunciaremos esse suporte quando ele estiver disponível.

Depois de criar uma conta do Azure Cosmos DB com suporte a vários mestres, você poderá criar bancos de dados e contêineres, carregar documentos e atribuir políticas de resolução de conflitos. Para saber mais sobre resolução de conflitos em vários mestres e exemplos de códigos, confira o artigo [Exemplos de código de vários mestres](multi-master-conflict-resolution.md#code-samples).

## <a name="enable-multi-master-using-azure-portal"></a>Habilitar vários mestres usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Clique em Selecionar **Criar um recurso > Bancos de dados > Azure Cosmos DB**.

3. N painel **Nova Conta**, insira as seguintes configurações para uma nova conta do Azure Cosmos DB:

   |**Configuração**  |**Valor sugerido** |**Descrição**|
   |---------|---------|---------|
   |Assinatura   | {Sua assinatura}  |Selecione a assinatura do Azure a ser usada para essa conta do Azure Cosmos DB.  |
   |Grupo de recursos  |   {O nome do seu grupo de recursos}    |  Selecione um grupo de recursos existente ou selecione  **Criar** e, em seguida, insira o nome de um novo grupo de recursos da conta. |
   |Nome da conta | {nome da sua conta}   |  Insira um nome exclusivo para identificar a conta do Azure Cosmos DB.        |
   |API  |   Qualquer   |  Selecione um tipo de API.   |
   |Local padrão  | Selecionar qualquer região   | Selecione a localização geográfica na qual hospedar a sua conta do Azure Cosmos DB. Você pode escolher qualquer região, pois essa conta estará em várias regiões.  |
   |Habilitar a redundância geográfica   |  Habilitar  |  Selecione para habilitar a opção vários mestres a ser selecionada abaixo.   |
   |Habilitar vários mestres | Habilitar  | Selecione para habilitar vários mestres para esta conta. |


## <a name="using-multi-master-in-sdks"></a>Usando vários mestres em SDKs

Com uma conta habilitada para vários mestres, dentro dos aplicativos, você pode aproveitar a opção de vários mestres com o ConnectionPolicy, conforme mostrado abaixo.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add("West US");
policy.PreferredLocations.Add("North Europe");
policy.PreferredLocations.Add("Southeast Asia");
```

## <a name="enable-multi-master-using-powershell"></a>Habilitar vários mestres usando o PowerShell

Você também pode criar uma conta do Cosmos DB habilitada para vários mestres definindo o parâmetro `enableMultipleWriteLocations` como "true". Para criar uma conta do Azure Cosmos DB com a opção vários mestres habilitada, abra uma janela do PowerShell e execute o seguinte script:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Habilitar vários mestres usando a CLI

Você pode habilitar vários mestres definindo o parâmetro enable-multiple-write-locations como "true". Para criar uma conta do Azure Cosmos DB com vários mestres habilitados, abra a CLI do Azure ou o Cloud Shell e execute o seguinte comando:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Habilitar o uso de vários mestres usando o modelo do Resource Manager

O seguinte código JSON é um modelo do Resource Manager de exemplo que você pode usar para implantar uma conta do Azure Cosmos DB. Para saber mais sobre o formato do modelo do Resource Manager e a sintaxe, confira a documentação do [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). O parâmetro de chave a ser observado nesse modelo é "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a habilitar o suporte a vários mestres em contas do Azure Cosmos DB. Em seguida, examine os seguintes recursos:

* [Usando vários mestres com bancos de dados NoSQL de software livre](multi-master-oss-nosql.md)

* [Noções básicas sobre a resolução de conflitos no Azure Cosmos DB](multi-master-conflict-resolution.md)
