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
ms.openlocfilehash: 0f64bcecadf5979e9983028354c41457771020bd
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129196"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Criar um cluster Kubernetes habilitado para Azure Dev Spaces

1. Entre no Portal do Azure em http://portal.azure.com.
1. Escolha **Criar um recurso** > procure por **Kubernetes** > selecione **Serviço de Kubernetes** > **Criar**.

   Conclua as seguintes etapas sob cada título do formulário de criação de cluster AKS.

    - **DETALHES DO PROJETO**: selecione uma assinatura do Azure e um grupo de recursos do Azure novo ou existente.
    - **DETALHES do CLUSTER**: insira um nome, região (no momento, você deve escolher EastUS, Centro dos EUA, WestEurope, WestUS2, CanadaCentral ou CanadaEast), versão e o prefixo de nome DNS para o cluster AKS.
    - **ESCALA**: selecione um tamanho de VM para os nós de agente AKS e o número de nós. Se você não tiver experiência com o Azure Dev Spaces, um nó é o suficiente para explorar todos os recursos. A contagem de nós pode ser facilmente ajustada a qualquer momento após a implantação do cluster. Observe que o tamanho da VM não pode ser alterado após a criação de um cluster AKS. No entanto, quando um cluster AKS tiver sido implantado, você pode facilmente criar um novo cluster AKS com VMs maiores e usar o Dev Spaces para reimplantar nesse cluster maior se você precisar escalar verticalmente.

   Escolha o Kubernetes versão 1.10.3 ou posterior.

   ![Definições de configuração do Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Selecione **Avançar: Autenticação** ao concluir.

1. Escolha a configuração desejada para o RBAC (Controle de Acesso Baseado em Função). O Azure Dev Spaces dá suporte a clusters com RBAC habilitado ou desabilitado.

    ![Configuração de RBAC](../media/common/k8s-RBAC.PNG)

1. Certifique-se de que o Roteamento de aplicativo Http está habilitado.

   ![Habilitar Roteamento de aplicativo HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Você deve certificar-se de habilitar o roteamento de aplicativo Http ao criar o cluster AKS. Não é possível alterar esta configuração depois.

1. Selecione **Revisar+ Criar** e depois **Criar** quando terminar.
