---
title: Criar função do Avere sem um controlador – Avere vFXT para Azure
description: Método para criar a função RBAC necessária sem uma VM do controlador de cluster
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670023"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Criar a função de acesso de tempo de execução do cluster do Avere vFXT sem um controlador

Este documento mostra um método para criar a função de acesso do nó de cluster na linha de comando antes de criar a VM do controlador de cluster. 

Para criá-la no controlador de cluster, leia [Criar a função de acesso do nó de cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role). A imagem do controlador inclui um arquivo de protótipo da função. Você pode atualizar o arquivo com sua ID da assinatura e usá-lo para definir a função localmente na VM do controlador.

## <a name="create-an-azure-rbac-role"></a>Criar uma função RBAC do Azure

O sistema do Avere vFXT usa [RBAC](https://docs.microsoft.com/azure/role-based-access-control/) (controle de acesso baseado em função) para autorizar os nós do cluster de vFXT a executarem as tarefas necessárias.  

Como parte da operação normal do cluster de vFXT, os nós do vFXT individuais precisam fazer coisas como ler propriedades de recursos do Azure, gerenciar o armazenamento e controlar as configurações do adaptador de rede de outros nós. 

Essa função é usada somente para os nós vFXT, não para a VM do controlador de cluster.  

Se quiser criar a função antes do controlador, siga estas etapas: 

1. Abra o Azure Cloud Shell no portal do Azure e navegue até [https://shell.azure.com](https://shell.azure.com).

1. Use o comando da CLI do Azure para alternar para sua assinatura do vFXT:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Use estes comandos para baixar a definição da função da imagem do marketplace e editá-la. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Edite o arquivo para incluir sua ID da assinatura e exclua a linha em cima dela. Salve o arquivo como ``avere-cluster.json``.

   ![Editor de texto do console mostrando a ID da assinatura e "remover esta linha" selecionado para exclusão](media/avere-vfxt-edit-role.png)

5. Crie a função:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Ao criar o cluster, forneça o nome da função (neste caso, `avere-cluster`). O script de criação do cluster atribui a função para os nós de cluster recém-criados. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Exemplo de definição da função de tempo de execução do nó de cluster

> [!IMPORTANT] 
> Esse exemplo de definição foi obtido de uma versão pré-GA do produto. Se a versão que você receber com a distribuição atual do produto for diferente, use essa versão.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```