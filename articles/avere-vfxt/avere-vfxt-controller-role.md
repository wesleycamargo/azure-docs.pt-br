---
title: Função de acesso de controlador personalizado – Avere vFXT para Azure
description: Como criar uma função de acesso personalizada para o controlador de cluster Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 7ed20ccac879ec8eba1c3fbd91e38d05b08a12d2
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302897"
---
# <a name="customized-controller-access-role"></a>Função de acesso de controlador personalizado

O controlador de cluster Avere vFXT para Azure usa uma identidade gerenciada e o RBAC (controle de acesso baseado em função) para permitir que ele crie e gerencie o cluster. 

Por padrão, o controlador de cluster recebe a atribuição da [função de Proprietário interna](../role-based-access-control/built-in-roles.md#owner). Além disso, o acesso do controlador é restrito ao seu grupo de recursos: ele não pode modificar os elementos fora do grupo de recursos do cluster.

Este artigo explica como criar sua própria função de acesso para o controlador de cluster em vez de usar a configuração padrão. 

## <a name="edit-the-role-prototype"></a>Editar o protótipo de função

Comece com a função de protótipo, disponível em <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Adicione a ID da assinatura para a implantação do Avere vFXT para Azure na instrução AssignableScopes. Personalize o nome e adicione ou altere as definições conforme necessário. 

Tenha cuidado se você restringir privilégios. A criação do cluster poderá falhar se o controlador não tiver acesso suficiente. 

Para ajudar a entender quais privilégios o controlador de cluster precisa para criar um cluster, [abra um tíquete de suporte](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Salve sua definição de função personalizada como um arquivo .json. 

## <a name="define-the-role"></a>Definir a função 

Siga estas etapas para adicionar a definição de função personalizada à sua assinatura. 

1. Abra o Azure Cloud Shell no portal do Azure e navegue até [https://shell.azure.com](https://shell.azure.com).

1. Use o comando da CLI do Azure para alternar para sua assinatura do vFXT:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Crie a função:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Use seu nome de arquivo e o caminho no lugar de ```/avere-contributor-custom.json``` neste exemplo. 

Salve a saída do comando de definição de função, ele contém o identificador de função que você precisa fornecer para o modelo de criação do cluster. 

## <a name="find-the-role-id"></a>Localizar a ID da função

O modelo de implantação do Avere vFXT precisa do GUID (identificador global exclusivo) da função para atribuir uma função personalizada ao controlador. 

O GUID da função é uma cadeia de caracteres de 32 caracteres neste formato: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Para pesquisar o GUID da sua função, use este comando com o nome da função no parâmetro ```--name```.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Insira essa cadeia de caracteres no campo **ID da função de criação de cluster do Avere** ao implantar o Avere vFXT para Azure.

## <a name="next-steps"></a>Próximas etapas

Leia como implantar o Avere vFXT para Azure em [Implantar cluster vFXT](avere-vfxt-deploy.md)
