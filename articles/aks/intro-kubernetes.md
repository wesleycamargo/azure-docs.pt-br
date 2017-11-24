---
title: "Introdução ao Serviço de Contêiner do Azure para Kubernetes | Microsoft Docs"
description: "O Serviço de Contêiner do Azure para Kubernetes simplifica a implantação e o gerenciamento de aplicativos baseados em contêiner no Azure."
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, Contêineres, microsserviços, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 9fba9fdda3503ec80fede845466858825e3677a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Introdução ao Serviço de Contêiner do Azure (AKS)

O Serviço de Contêiner do Azure (AKS) simplifica a criação, a configuração e o gerenciamento de um cluster de máquinas virtuais pré-configuradas para executar os aplicativos em contêineres. Isso permite que você use suas habilidades existentes ou explore uma experiência cada vez maior da comunidade para implantar e gerenciar aplicativos baseados em contêiner no Microsoft Azure.

Usando o AKS, você pode aproveitar as vantagens dos recursos de nível empresarial do Azure e ainda manter a portabilidade do aplicativo pelo formato de imagem do Docker e Kubernetes.

## <a name="managed-kubernetes-in-azure"></a>Kubernetes Gerenciado no Azure

O AKS reduz a complexidade e a sobrecarga operacional de gerenciar um cluster Kubernetes deixando grande parte dessa responsabilidade para o Azure. Como um serviço Kubernetes hospedado, o Azure lida com as tarefas críticas para você, como o monitoramento da integridade e a manutenção. Além disso, você paga apenas pelos nós do agente dentro de seus clusters, não pelos mestres. Como um serviço Kubernetes gerenciado, o AKS fornece:

> [!div class="checklist"]
> * Atualizações da versão do Kubernetes e correções automatizadas
> * Dimensionamento fácil do cluster
> * Plano de controle hospedado com autorrecuperação (mestres)
> * Redução de custos - pague somente pelos nós do pool de agentes em execução

Com o Azure lidando com o gerenciamento de nós no cluster AKS, você não precisa mais executar muitas tarefas manualmente, como as atualizações do cluster. Como o Azure lida com essas tarefas de manutenção importantes para você, o AKS não fornece um acesso direto (como no SSH) ao cluster.

## <a name="using-azure-container-service-aks"></a>Uso do Serviço de Contêiner do Azure (AKS)
O objetivo do AKS é fornecer um ambiente de hospedagem de contêineres usando ferramentas e tecnologias de fonte aberta que são comuns entre os clientes hoje. Para esse fim, vamos expor os pontos de extremidade da API do Kubernetes padrão. Usando esses pontos de extremidade padrão, é possível utilizar qualquer software que possa se comunicar com um cluster Kubernetes. Por exemplo, você pode escolher [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/), ou [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Criação de um cluster Kubernetes usando o Serviço de Contêiner do Azure (AKS)
Para começar a usar AKS, implante um cluster AKS com a [CLI do Azure](./kubernetes-walkthrough.md) ou por meio do portal (pesquise **Serviço de Contêiner do Azure** no Marketplace). Se você for um usuário avançado que precisa de mais controle sobre os modelos do Azure Resource Manager, poderá usar o projeto [acs-engine](https://github.com/Azure/acs-engine) de software livre para criar seu próprio cluster Kubernetes personalizado e implantá-lo por meio da CLI `az`.

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

## <a name="videos"></a>Vídeos

AKS (Serviço de Contêiner do Azure) – Azure Friday, outubro de 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Ferramentas para desenvolver e implantar aplicativos no Kubernetes – Azure OpenDev, junho de 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Saiba mais sobre como implantar e gerenciar o AKS com os tutoriais de início rápido do AKS.

> [!div class="nextstepaction"]
> [Tutorial do AKS](./kubernetes-walkthrough.md)