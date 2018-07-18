---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 6d2940712f8a76173de47badd45d7c7f0f0be05c
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825469"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Criar um cluster Kubernetes habilitado para Azure Dev Spaces

1. Entre no Portal do Azure em http://portal.azure.com.
1. Escolha **Criar um recurso** > procure por **Kubernetes** > selecione **Serviço de Kubernetes** > **Criar**.

   Conclua as seguintes etapas sob cada título do formulário de criação de cluster AKS.

    - **DETALHES DO PROJETO**: selecione uma assinatura do Azure e um grupo de recursos do Azure novo ou existente.
    - **DETALHES do CLUSTER**: insira um nome, região (no momento, você deve escolher Leste dos EUA, Europa Ocidental ou Leste do Canadá), versão e o prefixo de nome DNS para o cluster AKS.
    - **ESCALA**: selecione um tamanho de VM para os nós de agente AKS e o número de nós. Se você não tiver experiência com o Azure Dev Spaces, um nó é o suficiente para explorar todos os recursos. A contagem de nós pode ser facilmente ajustada a qualquer momento após a implantação do cluster. Observe que o tamanho da VM não pode ser alterado após a criação de um cluster AKS. No entanto, quando um cluster AKS tiver sido implantado, você pode facilmente criar um novo cluster AKS com VMs maiores e usar o Dev Spaces para reimplantar nesse cluster maior se você precisar escalar verticalmente.

   Certifique-se de escolher o Kubernetes versão 1.9.6 ou posterior.

   ![Definições de configuração do Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Selecione **Próximo: Rede** quando concluir.

1. Certifique-se de que o Roteamento de aplicativo Http está habilitado.

   ![Habilitar Roteamento de aplicativo HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Você deve certificar-se de habilitar o roteamento de aplicativo Http ao criar o cluster AKS. Não é possível alterar esta configuração depois.

1. Selecione **Revisar+ Criar** e depois **Criar** quando terminar.
