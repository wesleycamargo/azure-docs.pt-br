---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 93440b8a1c9fd1b386931e5998c70133071a079e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823037"
---
# <a name="azure-dev-spaces"></a>Espaços de Desenvolvimento do Azure
O Azure Dev Spaces fornece uma experiência de desenvolvimento Kubernetes rápida e iterativa para equipes. Com algumas poucas configurações de máquina de desenvolvimento, você pode executar e depurar contêineres de forma iterativa diretamente no Azure Kubernetes Service (AKS). Desenvolva no Windows, Mac ou Linux usando ferramentas familiares como o Visual Studio, Visual Studio Code ou a linha de comando.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como Azure Dev Spaces simplifica o desenvolvimento de Kubernetes 

O Azure Dev Spaces ajuda as equipes de desenvolvimento a serem mais produtivas no Kubernetes das seguintes maneiras:
- Minimize a configuração da máquina de desenvolvimento local para cada membro da equipe e trabalhe diretamente no AKS, um cluster Kubernetes gerenciado no Azure.
- Faça a iteração e depure rapidamente o código diretamente no Kubernetes usando o Visual Studio 2017 ou o Visual Studio Code.
- Gere os ativos de configuração como código do Docker e Kubernetes para uso desde o desenvolvimento até a produção. 
- Compartilhe um cluster gerenciado de Kubernetes com sua equipe e trabalhe de forma colaborativa. Desenvolver o código em isolamento e realizar testes de ponta a ponta com outros componentes sem replicar ou usar modelos das dependências.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>Veja também

[Serviço de Kubernetes do Azure](/azure/aks)