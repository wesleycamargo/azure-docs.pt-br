---
title: Introdução ao Serviço de Kubernetes do Azure
description: O Serviço de Kubernetes do Azure simplifica a implantação e o gerenciamento de aplicativos baseados em contêiner no Azure.
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4a36809b0f6a041ac4d9250624495aeaf1e397e9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="introduction-to-azure-kubernetes-service-aks-preview"></a>Introdução à versão prévia do Serviço de Kubernetes do Azure (AKS)

O Serviço de Kubernetes do Azure (AKS) simplifica a criação, a configuração e o gerenciamento de um cluster de máquinas virtuais pré-configuradas para executar os aplicativos em contêineres. Isso permite que você use suas habilidades existentes ou explore uma experiência cada vez maior da comunidade para implantar e gerenciar aplicativos baseados em contêiner no Microsoft Azure.

Usando o AKS, você pode aproveitar as vantagens dos recursos de nível empresarial do Azure e ainda manter a portabilidade do aplicativo pelo formato de imagem do Docker e Kubernetes.

> [!IMPORTANT]
> O Serviço de Kubernetes do Azure (AKS) está atualmente em **versão prévia**. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

## <a name="managed-kubernetes-in-azure"></a>Kubernetes Gerenciado no Azure

O AKS reduz a complexidade e a sobrecarga operacional de gerenciar um cluster Kubernetes deixando grande parte dessa responsabilidade para o Azure. Como um serviço Kubernetes hospedado, o Azure lida com as tarefas críticas para você, como o monitoramento da integridade e a manutenção. Além disso, você paga apenas pelos nós do agente dentro de seus clusters, não pelos mestres. Como um serviço Kubernetes gerenciado, o AKS fornece:

> [!div class="checklist"]
> * Atualizações da versão do Kubernetes e correções automatizadas
> * Dimensionamento fácil do cluster
> * Plano de controle hospedado com autorrecuperação (mestres)
> * Redução de custos - pague somente pelos nós do pool de agentes em execução

Com o Azure lidando com o gerenciamento de nós no cluster AKS, você não precisa mais executar muitas tarefas manualmente, como as atualizações do cluster. Como o Azure lida com essas tarefas de manutenção importantes para você, o AKS não fornece um acesso direto (como no SSH) ao cluster.

## <a name="using-azure-kubernetes-service-aks"></a>Usar o AKS (Serviço do Kubernetes do Azure)
O objetivo do AKS é fornecer um ambiente de hospedagem de contêineres usando ferramentas e tecnologias de fonte aberta que são comuns entre os clientes hoje. Para esse fim, vamos expor os pontos de extremidade da API do Kubernetes padrão. Usando esses pontos de extremidade padrão, é possível utilizar qualquer software que possa se comunicar com um cluster Kubernetes. Por exemplo, você pode escolher [kubectl][kubectl-overview], [helm][helm] ou [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-kubernetes-service-aks"></a>Criação de um cluster Kubernetes usando o Serviço de Kubernetes do Azure (AKS)
Para começar a usar AKS, implante um cluster AKS com a [CLI do Azure][aks-quickstart] ou por meio do portal (pesquise **Serviço de Kubernetes do Azure** no Marketplace). Se você for um usuário avançado que precisa de mais controle sobre os modelos do Azure Resource Manager, use o projeto [acs-engine][acs-engine] de código aberto para criar seu próprio cluster Kubernetes personalizado e implantá-lo por meio da CLI `az`.

### <a name="using-kubernetes"></a>Como usar Kubernetes
O Kubernetes automatiza a implantação, o dimensionamento e o gerenciamento de aplicativos em contêineres. Ele tem um conjunto avançado de recursos, incluindo:
* Binpacking automático
* Autorrecuperação
* Dimensionamento em escala horizontal
* Descoberta de serviço e balanceamento de carga
* Reversões e distribuições automatizadas
* Segredos e gerenciamento de configuração
* Orquestração de armazenamento
* Execução do Lote

## <a name="videos"></a>vídeos

AKS (Serviço de Kubernetes do Azure) – Azure Friday, outubro de 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Ferramentas para desenvolver e implantar aplicativos no Kubernetes – Azure OpenDev, junho de 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Saiba mais sobre como implantar e gerenciar o AKS com os tutoriais de início rápido do AKS.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

