---
title: Visão geral da implantação - Avere vFXT for Azure
description: Visão geral da implantação do Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669813"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT para Azure - visão geral da implantação

Este artigo fornece uma visão geral das etapas necessárias para ativar e executar um cluster do Avere vFXT para Azure.

A primeira vez que você implanta um sistema Avere vFXT, pode perceber que envolve mais etapas do que a implantação da maioria das outras ferramentas do Azure. Ter uma noção clara do processo de início ao fim irá ajudá-lo a definir o esforço necessário. Depois que o sistema estiver em funcionamento, seu poder de agilizar as tarefas de computação baseadas na nuvem valerá a pena o esforço.

## <a name="deployment-steps"></a>Etapas de implantação.

Após [planejar seu sistema](avere-vfxt-deploy-plan.md), você pode começar a criar seu cluster Avere vFXT. 

Comece criando uma VM do controlador de cluster, que é usada para criar o cluster vFXT.

Depois que o cluster vFXT estiver em execução, você precisará saber como conectar clientes a ele e, se necessário, como mover seus dados para o novo contêiner de armazenamento do Blob.  

Aqui está uma visão geral de todas as etapas.

1. Configurar pré-requisitos 

   Antes de criar uma VM, você deve criar uma nova assinatura para o projeto Avere vFXT, configurar a propriedade da assinatura, verificar cotas e solicitar um aumento, se necessário, e aceitar os termos para usar o software Avere vFXT. Leia [Preparar para criar o Avere vFXT](avere-vfxt-prereqs.md) para instruções detalhadas.

1. Crie o controlador de cluster

   O *controlador de cluster* é uma VM simples que reside na mesma rede virtual que o cluster Avere vFXT. O controlador cria os nós vFXT e forma o cluster e também fornece uma interface de linha de comandos para gerenciar o cluster durante sua existência.

   Se você configurar seu controlador com um endereço IP público, ele também poderá servir como um host de salto para se conectar ao cluster de fora da vnet.

   Todo o software necessário para criar o cluster vFXT e gerenciar seus nós é pré-instalado no controlador de cluster.

   Leia [Crie a VM do controlador de cluster](avere-vfxt-deploy.md#create-the-cluster-controller-vm) para obter detalhes.

1. Criar uma função de tempo de execução para os nós de cluster 

   O Azure usa [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) para autorizar as VMs do nó do cluster a executar determinadas tarefas. Por exemplo, os nós do cluster precisam poder atribuir ou reatribuir endereços IP a outros nós do cluster. Antes de criar o cluster, você deve definir uma função que forneça as permissões adequadas.

   O software pré-instalado do controlador de cluster inclui uma função de protótipo para você personalizar. Leia [Crie a função de acesso do nó do cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role) para obter instruções.

1. Crie o cluster Avere vFXT 

   No controlador, edite o script de criação de cluster apropriado e execute-o para criar o cluster. [Edite o script de implantação](avere-vfxt-deploy.md#edit-the-deployment-script) com instruções detalhadas. 

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