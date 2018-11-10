---
title: Pré-requisitos de Avere vFXT – Azure
description: Pré-requisitos do Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669523"
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
* Permitir que o nó do controlador de cluster gerencie grupos de recursos e redes virtuais 
* Permitir que o controlador de cluster crie e modifique os nós de cluster 

Há duas soluções alternativas, se você não desejar fornecer acesso de proprietário para os usuários que criarem o vFXT:

* Um proprietário do grupo de recursos poderá criar um cluster se estas condições forem atendidas:

  * Um proprietário de assinatura precisa [aceitar os termos do software Avere vFXT](#accept-software-terms-in-advance) e [criar a função de acesso do nó de cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Todos os recursos do Avere vFXT precisam ser implantados dentro do grupo de recursos, incluindo:
    * Controlador do cluster
    * Nós de cluster
    * Armazenamento de blob
    * Elementos de rede
 
* Um usuário sem privilégios de proprietário pode criar clusters de vFXT se uma função de acesso extra é criada e atribuída ao usuário antes do tempo. No entanto, essa função fornece permissões significativas para esses usuários. [Este artigo](avere-vfxt-non-owner.md) explica como autorizar não proprietários a criar clusters.

## <a name="quota-for-the-vfxt-cluster"></a>Cota para o cluster vFXT

Você precisa ter uma cota suficiente para os componentes do Azure a seguir. Se necessário, [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> As máquinas virtuais e componentes SSD aqui listados são para o próprio cluster vFXT. Você precisará de cota adicional para as VMs e o SSD que pretender usar para seu farm de computação.  Verifique se a cota está habilitada para a região em que você pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|Três ou mais D16s_v3 ou E32s_v3|
|Armazenamento SSD Premium|Espaço do sistema operacional de 200 GB, além de 1 a 4 TB de espaço do cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contêiner de Blob de LRS |

## <a name="accept-software-terms-in-advance"></a>Aceitar termos de software de antemão

> [!NOTE] 
> Essa etapa não será necessária se um proprietário de assinatura criar o cluster do Avere vFXT.

Antes de poder criar um cluster, você precisa aceitar os termos de serviço do software Avere vFXT. Se você não é um proprietário de assinatura, faça com que um proprietário de assinatura aceite os termos de antemão. Essa etapa só precisa ser feita uma vez por assinatura.

Para aceitar os termos de software de antemão: 

1. Abra um Cloud Shell no portal do Azure ou navegando até <https://shell.azure.com>. Entre com sua ID da assinatura.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Execute este comando para aceitar os termos de serviço e habilitar o acesso programático para o Avere vFXT para imagens de software do Azure: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>Próxima etapa: criar o cluster vFXT

Após concluir esses pré-requisitos, você pode passar à criação do cluster em si. Leia [Implantar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.