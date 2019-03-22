---
title: Visão geral da implantação - Avere vFXT for Azure
description: Visão geral da implantação do Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0c61db5e34ba58bb767b0bda773a54c8e65cd404
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991794"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para Azure - visão geral da implantação

Este artigo fornece uma visão geral das etapas necessárias para ativar e executar um cluster do Avere vFXT para Azure.

Várias tarefas são necessárias antes e depois de criar o cluster vFXT do Azure Marketplace. Ter uma noção clara do processo de início ao fim irá ajudá-lo a definir o esforço necessário. 

## <a name="deployment-steps"></a>Etapas de implantação.

Após [planejar seu sistema](avere-vfxt-deploy-plan.md), você pode começar a criar seu cluster Avere vFXT. 

Um modelo do Azure Resource Manager no Azure Marketplace coleta as informações necessárias e implanta automaticamente todo o cluster. 

Depois que o cluster vFXT estiver em execução, você precisará saber como conectar clientes a ele e, se necessário, como mover seus dados para o novo contêiner de armazenamento do Blob.  

Aqui está uma visão geral de todas as etapas.

1. Configurar pré-requisitos 

   Antes de criar uma VM, você deve criar uma nova assinatura para o projeto Avere vFXT, configurar a propriedade da assinatura, verificar cotas e solicitar um aumento, se necessário, e aceitar os termos para usar o software Avere vFXT. Leia [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md) para instruções detalhadas.

1. Crie uma função de acesso para os nós de cluster

   O Azure usa [controle de acesso baseado em função](../role-based-access-control/index.yml) (RBAC) para autorizar as VMs do nó do cluster a executar determinadas tarefas. Por exemplo, os nós do cluster precisam poder atribuir ou reatribuir endereços IP a outros nós do cluster. Antes de criar o cluster, você deve definir uma função que forneça as permissões adequadas.

   Leia [Crie a função de acesso do nó do cluster](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) para obter instruções.

   O controlador de cluster também usa uma função de acesso, mas você pode aceitar a função padrão, Proprietário, em vez de criar sua própria. Se você quiser criar uma função personalizada para o controlador de cluster, leia [Função de acesso de controlador personalizada](avere-vfxt-controller-role.md). 

1. Crie o cluster Avere vFXT 

   Use o Azure Marketplace para criar o cluster Avere vFXT para Azure. Um modelo coleta as informações necessárias e executa scripts para criar o produto final.

   A criação do cluster envolve estas etapas, que são todas realizadas pelo modelo do marketplace: 

   * Criar nova infraestrutura de rede e grupos de recursos se necessário
   * Criar um *controlador do cluster*  

     O controlador de cluster é uma VM simples do que reside na mesma rede virtual que o cluster do Avere vFXT e tem o software personalizado necessário para criar e gerenciar o cluster. O controlador cria os nós vFXT e forma o cluster e também fornece uma interface de linha de comandos para gerenciar o cluster durante sua existência.

     Se você criar uma nova rede virtual durante a implantação, seu controlador terá um endereço IP público. Isso significa que o controlador pode servir como um host de salto para conectar-se ao cluster de fora da vnet.

   * Criar as VMs do nó de cluster

   * Configurar as VMs para formar o cluster do nó de cluster

   * Opcionalmente, criar um novo contêiner de Blob e configurá-lo como um armazenamento de back-end para o cluster

1. Configurar o cluster 

   Conecte-se à interface de configuração do Avere vFXT (Avere Control Panel) para personalizar as configurações do cluster. Opte pelo monitoramento de suporte e adicione seu sistema de armazenamento se você estiver usando um datacenter local.

   * [Acessar o cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Habilitar o suporte](avere-vfxt-enable-support.md)
   * [Configurar o armazenamento](avere-vfxt-add-storage.md) (se necessário)

1. Clientes de montagem

   Siga as diretrizes em [Montar o cluster Avere vFXT](avere-vfxt-mount-clients.md) para aprender sobre o balanceamento de carga e como as máquinas clientes devem montar o cluster.

1. Adicione dados (se necessário)

   Como o Avere vFXT é um cache escalonável de vários clientes, a melhor maneira de mover dados para um novo contêiner de armazenamento de back-end é com a estratégia de cópia de arquivos multithread e de vários clientes. Leia [Mover dados para o cluster vFXT](avere-vfxt-data-ingest.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

Continue em [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md) para concluir as tarefas preliminares para implantar o Avere vFXT para Azure. 