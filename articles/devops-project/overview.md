---
title: Visão geral do projeto Azure DevOps | Microsoft Docs
description: Entender o valor do projeto Azure DevOps
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: b87cb14fc707d14638c2d6a52af6f295276a2279
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="overview-of-azure-devops-project"></a>Visão geral do projetos do Azure DevOps

O projeto do Azure DevOps facilita o uso inicial do Azure. O recurso de projeto DevOps ajuda você a iniciar seu tipo de aplicativo favorito no serviço do Azure de sua escolha em apenas algumas etapas rápidas do Portal do Azure. O projeto DevOps configura tudo o que você precisa para desenvolver, implantar e monitorar seu aplicativo.
O painel de projeto DevOps permite o monitoramento de confirmações, compilações e implantações de código, tudo a partir de um único modo de exibição no Portal do Azure.

## <a name="why-should-i-use-the-azure-devops-project"></a>Por que devo usar o projeto Azure DevOps?

O projeto Azure DevOps automatiza a instalação de um pipeline inteira de CI (integração contínua) e CD (entrega contínua) para o Azure.  Comece com seu código existente ou use um dos aplicativos de exemplo fornecidos e, depois, implante rapidamente o aplicativo em vários serviços do Azure, como Máquinas Virtuais, Serviço de Aplicativo, Serviço de Contêiner do Azure, Banco de Dados SQL do Azure e o Azure Service Fabric.  

O projeto Azure DevOps faz todo o trabalho de configuração inicial de um pipeline de DevOps, incluindo tudo desde a configuração do repositório inicial do Git, configuração do pipeline de CI/CD, criação de um recurso do Application Insights para monitorar e fornecer uma única exibição de toda a solução com a criação de um painel de projeto do Azure DevOps no Portal do Azure.

Você pode usar o projeto Azure DevOps:

* Implantar rapidamente seu aplicativo no Azure
* Automatizar a configuração de um pipeline CI/CD de VSTS
* Use o projeto DevOps como um modelo para exibir e compreender como configurar corretamente o CI/CD para o Azure com VSTS
* Introdução ao pipeline de CI/CD para o Azure e personalize o pipeline de versão com base em seus cenários específicos

## <a name="how-do-i-use-the-azure-devops-project"></a>Como usar o projeto Azure DevOps?

O projeto Azure DevOps está disponível no Portal do Azure.  Você pode criar um recurso de projeto do Azure DevOps assim como qualquer outro recurso do Azure no portal.  O projeto DevOps fornece uma experiência passo a passo do tipo assistente para as várias opções de configuração.  

Você pode escolher várias opções de configuração como parte da instalação inicial.  Estas opções incluem:

* Usar o aplicativo de exemplo fornecido, ou trazer seu próprio código
* Selecione uma linguagem de aplicativo
* Escolha uma estrutura de aplicativo com base na linguagem
* Selecione um serviço do Azure (destino de implantação)
* Conta do VSTS (nova ou existente)
* Escolha a sua assinatura do Azure
* Escolha o local dos serviços do Azure
* Escolhe entre vários tipos de preços para os serviços do Azure

Depois de usar o projeto Azure DevOps, você também poderá excluir todos os recursos de um único local a partir do painel do projeto Azure DevOps no Portal do Azure.

## <a name="azure-devops-project-and-vsts-integration"></a>Integração do projeto Azure DevOps com VSTS

Projetos de DevOps são capacitados pelo VSTS.  O projeto DevOps automatiza todo o trabalho necessário no VSTS para configurar CI/CD no Azure.  Um repositório Git é criado em uma conta do VSTS nova ou existente.  O projeto DevOps confirma um aplicativo de exemplo ou seu código existente para um novo repositório Git.  A automação também estabelece um gatilho de CI para compilação de modo que cada nova confirmação de código inicie uma compilação.  O projeto DevOps também cria um gatilho de CD e implantará cada nova compilação bem-sucedida no serviço do Azure de sua escolha.  As definições de build e versão podem ser personalizadas para cenários adicionais.  Também é possível clonar as definições de build e versão para uso em outros projetos.

Depois de criar seu projeto DevOps, você pode:

* Personalizar seu pipeline de compilação e versão
* Usar solicitações de pull para gerenciar o fluxo de seu código e manter a qualidade alta
* Teste e compile cada confirmação antes de mesclar seu código a fim de elevar o nível de qualidade
* Controle a lista de pendências do projeto e os problemas junto com seu aplicativo

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Como faço para começar a usar o projeto Azure DevOps?

* [Introdução ao projeto Azure DevOps](https://docs.microsoft.com/vsts/build-release/actions/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Vídeos sobre o projeto Azure DevOps

* [Criar CI/CD com o projeto Azure DevOps](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
