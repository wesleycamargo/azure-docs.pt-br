---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198751"
---
# <a name="azure-dev-spaces"></a>Espaços de Desenvolvimento do Azure
O Azure Dev Spaces ajuda você a desenvolver com maior rapidez no Kubernetes. Com o Azure Dev Spaces, você também adiciona recursos completos de desenvolvimento, como depuração, aos seus contêineres do Azure Kubernetes e você pode desenvolver contêineres de forma iterativa na nuvem usando ferramentas comuns como VS Code, Visual Studio ou linha de comando. O Azure Dev Spaces é especialmente relevante no desenvolvimento em equipe onde o isolamento de ramificações de código individuais em seus próprios espaços é uma parte crítica do ciclo de vida de desenvolvimento.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como Azure Dev Spaces simplifica o desenvolvimento de Kubernetes 

Essa abordagem possui vários benefícios:

* Obtenha um ambiente de desenvolvimento sem infra-estrutura que representa um ambiente de produção, com acesso total aos recursos de nuvem.
* Depure contêineres Node.js e .NET Core diretamente no Kubernetes com VS Code ou Visual Studio. Todos as outras linguagens podem ser desenvolvidas com a interface de linha de comando.
* Compartilhe uma instância de Kubernetes com sua equipe de desenvolvimento para reduzir custos e minimizar a configuração do computador local para novos membros da equipe.
* Desenvolver o código em isolamento e realizar testes de ponta a ponta com outros componentes sem replicar ou usar modelos das dependências.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)