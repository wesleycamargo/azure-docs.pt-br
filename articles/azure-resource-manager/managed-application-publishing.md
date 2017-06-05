---
title: Criar e publicar aplicativo gerenciado do Azure | Microsoft Docs
description: Mostra como um ISV ou parceiro cria um aplicativo gerenciado do Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>Criar e publicar um aplicativo gerenciado do Azure 

Conforme descrito no artigo [Visão geral de aplicativos gerenciados](managed-application-overview.md), há dois cenários na experiência de ponta a ponta. Um é o publicador ou ISV que queira criar um aplicativo gerenciado para uso pelos clientes. O outro é o cliente ou o cliente do aplicativo gerenciado. Este artigo se concentra no primeiro cenário e explica como um ISV pode criar e publicar um aplicativo gerenciado. 

Para criar um aplicativo gerenciado, você deverá criar:

* um pacote que contém os arquivos de modelo
* o usuário, grupo ou aplicativo que tem acesso ao grupo de recursos na assinatura do cliente
* a definição do dispositivo

Para obter exemplos dos arquivos, consulte [Exemplos de aplicativo gerenciado](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).

## <a name="create-managed-application-package"></a>Criar pacote de aplicativos gerenciados

A primeira etapa é criar o pacote de aplicativos gerenciados que contém os arquivos de modelo principais. O editor ou ISV cria três arquivos. 

* O primeiro arquivo é chamado **applianceMainTemplate.json**. Esse arquivo de modelo define os recursos reais que são provisionados como parte do aplicativo gerenciado. Por exemplo, para criar uma conta de armazenamento usando um aplicativo gerenciado, o applianceMainTemplate.json contém: 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
            "storageAccountName": {
                "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {        
          }
      }],
      "outputs": {        
      }
  }
  ```

* O segundo arquivo que o editor precisa criar é **mainTemplate.json**. O arquivo de modelo contém apenas o recurso de dispositivo (Microsoft.Solutions/appliances). Ele também contém todos os parâmetros necessários para os recursos de applianceMainTemplate.json. 

  Há duas propriedades importantes que são necessárias como entrada durante a criação do aplicativo gerenciado. A propriedade **managedResourceGroupId** é a ID do grupo de recursos onde os recursos definidos no applianceMainTemplate.json são criados. O formato da ID é:`/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  A propriedade **applianceDefinitionId** é a ID do recurso de definição de aplicativo gerenciado. A ID está no formato:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  Forneça parâmetros para esses dois valores para que o consumidor possa especificá-las ao criar um aplicativo gerenciado. No exemplo a seguir, os dois parâmetros que correspondem a essas propriedades são managedByResourceGroup e applianceDefinitonId.

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }                
              }
          }
      }]
  }
  ```

* O terceiro arquivo necessário no pacote é **createUiDefinition.json**. O portal do Azure usa esse arquivo para gerar a interface do usuário para os consumidores que estão criando o aplicativo gerenciado. Você define os parâmetros para o aplicativo gerenciado e como os consumidores podem obter a entrada para cada parâmetro. Você pode usar opções como seletor de lista suspensa, caixa de texto, caixa de senha e outras ferramentas de entrada. Para saber mais sobre como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).

Depois que todos os arquivos necessários estiverem prontos, carregue o pacote em um local de onde ele possa ser consumido.


## <a name="create-azure-ad-user-group-or-application"></a>Criar o aplicativo ou grupo de usuários do Azure AD
Em seguida, crie um grupo de usuário ou aplicativo que você deseja usar para gerenciar os recursos em nome do cliente. Este grupo de usuários ou aplicativo tem permissões para grupo de recursos gerenciados conforme descrito pela função. A função pode ser qualquer função RBAC interna, como **Proprietário** ou **Colaborador**. Um usuário individual também pode receber permissões para gerenciar os recursos, mas normalmente você atribui essa permissão para usar um grupo de usuários. Para criar um novo grupo de usuários do Active Directory, use:

```azurecli
az ad group create –display-name "name" –mail-nickname "nickname"
```

Você também pode usar um grupo existente. É necessário o ID do objeto do grupo de usuário existente ou recém-criado. O exemplo a seguir mostra como obter o ID do objeto do nome de exibição que foi usado para criar o grupo.

```azurecli
az ad group show –group "groupName"
```

Exemplo:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

Isso retorna a seguinte saída:

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
Você precisa do valor objectId acima. 

## <a name="get-the-role-definition-id"></a>Obter a ID de definição de função

Em seguida, é necessário o ID de definição de função da função RBAC interna para conceder acesso ao usuário, grupo de usuários ou aplicativo. Normalmente, seria ideal usar a função "Proprietário", "Colaborador" ou "Leitor". O comando abaixo mostra como obter a ID de definição de função para a função "Proprietário":

```azurecli
az role definition list --name owner
```

Isso retorna a seguinte saída:

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

Você precisa do valor da propriedade "name" do exemplo anterior.


## <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

A etapa final é criar o recurso de definição de aplicativo gerenciado. Depois de criar o pacote do dispositivo e as autorizações, crie a definição de dispositivo usando o seguinte comando: 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

Os parâmetros usados no exemplo anterior são:

- resource-group - o nome do grupo de recursos onde a definição do dispositivo é criada.
- lock-level - o tipo de bloqueio colocado no grupo de recursos gerenciado. Ela impede que o cliente execute operações indesejáveis no grupo de recursos. Atualmente, **ReadOnly** é o único nível de bloqueio com suporte. Quando ReadOnly é especificado, o cliente pode ler somente os recursos presentes no grupo de recursos gerenciados.
- authorizations - descreve a ID da entidade e a ID de definição de função que são usadas para conceder permissão ao grupo de recursos gerenciado. Ele é especificado no formato `<principalId>:<roleDefinitionId>`. Vários valores também podem ser especificados para essa propriedade. Se forem necessários vários valores, eles devem ser especificados neste formulário `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Vários valores são separados por um espaço.
- package-file-uri - o local do pacote do dispositivo que contém os arquivos de modelo, que podem ser um blob do Armazenamento do Azure. 


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](managed-application-overview.md).
* Para obter exemplos dos arquivos, consulte [Exemplos de aplicativo gerenciado](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para entender a experiência do usuário, consulte [Consumir um aplicativo gerenciado do Azure](managed-application-consumption.md).
* Para saber mais sobre como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](managed-application-createuidefinition-overview.md).
