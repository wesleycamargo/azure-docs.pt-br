---
title: Pré-requisitos de Avere vFXT – Azure
description: Pré-requisitos do Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 04af92f21cecaa832e857a7017b67f815f6ab685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417965"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o Avere vFXT

Este artigo explica as tarefas de pré-requisito para a criação de um cluster do Avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

Comece criando uma nova assinatura do Azure. Use uma assinatura separada para cada projeto do Avere vFXT para permitir que você acompanhe facilmente todos os recursos e as despesas do projeto, proteja outros projetos de limitação durante um possível provisionamento de recursos e simplifique a limpeza.  

Para criar uma nova assinatura do Azure no portal do Azure:

* Navegue até a [folha Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Clique no botão **+ Adicionar** na parte superior
* Entre se solicitado
* Selecione uma oferta e percorra as etapas para criar uma nova assinatura

## <a name="configure-subscription-owner-permissions"></a>Configure as permissões do proprietário da assinatura

Um usuário com permissões de proprietário para a assinatura deve criar o cluster do vFXT. Permissões de proprietário de assinatura são necessárias para estas ações, entre outras:

* Aceitar os termos do software Avere vFXT
* Criar a função de acesso do nó de cluster 

Há duas soluções alternativas, se você não desejar fornecer acesso de proprietário para os usuários que criarem o vFXT:

* Um proprietário do grupo de recursos poderá criar um cluster se estas condições forem atendidas:

  * Um proprietário de assinatura precisa [aceitar os termos do software Avere vFXT](#accept-software-terms) e [criar a função de acesso do nó de cluster](#create-the-cluster-node-access-role). 
  * Todos os recursos do Avere vFXT precisam ser implantados dentro do grupo de recursos, incluindo:
    * Controlador do cluster
    * Nós de cluster
    * Armazenamento de blob
    * Elementos de rede
 
* Um usuário sem privilégios de proprietário pode criar clusters do vFXT usando o RBAC (controle de acesso baseado em função) antecipadamente para atribuir privilégios ao usuário. Esse método fornece permissões significativas para esses usuários. [Este artigo](avere-vfxt-non-owner.md) explica como criar uma função de acesso para autorizar não proprietários a criar clusters.

## <a name="quota-for-the-vfxt-cluster"></a>Cota para o cluster vFXT

Você precisa ter uma cota suficiente para os componentes do Azure a seguir. Se necessário, [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> As máquinas virtuais e componentes SSD aqui listados são para o próprio cluster vFXT. Você precisará de cota adicional para as VMs e o SSD que pretender usar para seu farm de computação.  Verifique se a cota está habilitada para a região em que você pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|3 ou mais E32s_v3|
|Armazenamento SSD Premium|Espaço do sistema operacional de 200 GB, além de 1 a 4 TB de espaço do cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contêiner de Blob de LRS |

## <a name="accept-software-terms"></a>Aceitar os termos de software

> [!NOTE] 
> Essa etapa não será necessária se um proprietário de assinatura criar o cluster do Avere vFXT.

Durante a criação do cluster, você deve aceitar os termos de serviço para o software Avere vFXT. Se você não é um proprietário de assinatura, faça com que um proprietário de assinatura aceite os termos de antemão. Essa etapa só precisa ser feita uma vez por assinatura.

Para aceitar os termos de software de antemão: 

1. Abra um Cloud Shell no portal do Azure ou navegando até <https://shell.azure.com>. Entre com sua ID da assinatura.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Execute este comando para aceitar os termos de serviço e habilitar acesso programático para a imagem do software Avere vFXT para Azure: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-access-roles"></a>Criar funções de acesso 

O RBAC [Controle de acesso baseado em função](../role-based-access-control/index.yml) fornece autorização para o controlador de cluster vFXT e os nós do cluster executarem as tarefas necessárias.

* O controlador de cluster precisa de permissão para criar e modificar as VMs para criar o cluster. 

* Os nós do vFXT individuais precisam executar ações como ler propriedades de recursos do Azure, gerenciar o armazenamento e controlar as configurações do adaptador de rede de outros nós como parte da operação normal do cluster.

Antes de criar o cluster do Avere vFXT, você deve definir uma função personalizada para usar com os nós de cluster. 

Para o controlador de cluster, você pode aceitar a função padrão do modelo. O padrão fornece ao grupo de recursos do controlador de cluster privilégios de proprietário. Se você preferir criar uma função personalizada para o controlador, confira [Função de acesso de controlador personalizada](avere-vfxt-controller-role.md).

> [!NOTE] 
> Somente um proprietário da assinatura ou um usuário com a função de proprietário ou Administrador de Acesso do Usuário pode criar funções. As funções podem ser criadas com antecedência.  

### <a name="create-the-cluster-node-access-role"></a>Criar a função de acesso do nó de cluster

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Você deve criar a função de nó de cluster antes de criar o cluster Avere vFXT para Azure.

> [!TIP] 
> Os usuários internos da Microsoft devem usar a função existente chamada "Avere Cluster Runtime Operator" em vez de tentar criar uma. 

1. Copie esse arquivo. Adicione sua ID de assinatura na linha AssignableScopes.

   (A versão atual desse arquivo é armazenada no repositório github.com/Azure/Avere como [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/read",
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

1. Salve o arquivo como ``avere-operator.json`` ou um nome de arquivo similar fácil de lembrar. 


1. Abra um Azure Cloud Shell e entre com sua ID de assinatura (descrita [anteriormente neste documento](#accept-software-terms)). Use este comando para criar a função:

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

O nome da função é usado ao criar o cluster. Neste exemplo, o nome é ``avere-operator``.

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Criar um ponto de extremidade de serviço de armazenamento em sua rede virtual (se necessário)

Um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego de BLOBs do Azure local em vez de roteá-la fora da rede virtual. É recomendável para qualquer vFXT Avere para cluster do Azure que usa BLOBs do Azure para armazenamento de dados back-end. 

Se você estiver fornecendo uma rede virtual existente e criar um novo contêiner de BLOBs do Azure para seu armazenamento de back-end como parte da criação do cluster, você deve ter um ponto de extremidade de serviço na rede virtual para o armazenamento do Microsoft. Esse ponto de extremidade deve existir antes da criação do cluster, ou a criação falhará. 

Um ponto de extremidade de serviço de armazenamento é recomendado para qualquer vFXT Avere para cluster do Azure que usa o armazenamento de BLOBs do Azure, mesmo se você adicionar o armazenamento mais tarde. 

> [!TIP] 
> * Ignore esta etapa se você estiver criando uma nova rede virtual como parte da criação do cluster. 
> * Esta etapa é opcional se você não estiver criando o armazenamento de BLOBs durante a criação do cluster. Nesse caso, você pode criar o ponto de extremidade de serviço posteriormente se você decidir usar BLOBs do Azure.

Crie o ponto de extremidade de serviço de armazenamento do portal do Azure. 

1. No portal, clique em **Redes virtuais** à esquerda.
1. Selecione a rede virtual para seu cluster. 
1. Clique em **Pontos de extremidade de serviço** à esquerda.
1. Clique em **Adicionar** na parte superior.
1. Deixe o serviço como ``Microsoft.Storage`` e escolha a sub-rede do cluster.
1. Na parte inferior, clique em **Adicionar**.

   ![Captura de tela do portal do Azure com anotações para as etapas de criação de ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Próxima etapa: Criar o cluster vFXT

Após concluir esses pré-requisitos, você pode passar à criação do cluster em si. Leia [Implantar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.