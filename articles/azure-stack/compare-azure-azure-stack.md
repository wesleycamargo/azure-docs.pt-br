---
title: Comparar o Azure Stack e o Azure global | Microsoft Docs
description: Saiba como a Microsoft fornece a família do Azure Stack de serviços em um ecossistema do Azure e Azure
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650065"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Diferenças entre o Azure, Azure Stack e Azure Stack HCI global

A Microsoft fornece o Azure e a família do Azure Stack de serviços em um ecossistema do Azure. Use o mesmo modelo de aplicativo, portais de autoatendimento e APIs com o Azure Resource Manager para fornecer recursos de baseada em nuvem, se sua empresa usa o Azure global ou recursos locais.

Este artigo descreve os recursos globais do Azure, Azure Stack e o Azure Stack HCI e fornece recomendações de cenário comum para ajudá-lo a fazer a melhor escolha para fornecer serviços baseados em nuvem do Microsoft para sua organização.

![Visão geral do ecossistema do Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Azure Global

O Microsoft Azure é um conjunto cada vez maior de serviços de nuvem para ajudar sua organização a enfrentar seus desafios de negócios. É a liberdade de criar, gerenciar e implantar aplicativos em uma enorme rede global usando suas ferramentas e estruturas favoritas.

Global Azure oferece mais de 100 serviços disponíveis em 54 regiões em todo o mundo. Para obter a lista mais atual dos serviços globais do Azure, consulte a [ *produtos disponíveis por região*](https://azure.microsoft.com/regions/services). Os serviços disponíveis no Azure são listadas por categoria, bem como se eles estão geralmente disponíveis ou estão disponíveis por meio de visualização.

Para obter mais informações sobre serviços globais do Azure, consulte [Introdução ao Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

O Azure Stack é uma extensão do Azure que oferece a agilidade e a inovação da computação em nuvem para seu ambiente local. Implantado no local, Azure Stack pode ser usado para fornecer serviços consistentes do Azure, que um conectado à internet (e do Azure) ou em ambientes desconectados sem conectividade de internet. O Azure Stack usa as mesmas tecnologias subjacentes como Azure global, o que inclui os principais componentes da infraestrutura-como um serviço (IaaS), Software-como um serviço (SaaS) e recursos opcionais de plataforma-como um serviço (PaaS), incluindo:

- VMs do Azure para Windows e Linux
- Funções e aplicativos Web do azure
- Cofre da Chave do Azure
- Azure Resource Manager
- Azure Marketplace
- Contêineres
- O Hub IoT e Hubs de eventos
- Ferramentas de administração (planos, ofertas, RBAC, etc.)

Os recursos de PaaS do Azure Stack são opcionais, pois o Azure Stack não é operado pela Microsoft, ela é operada por nossos clientes. Isso significa que você pode oferecer qualquer serviço de PaaS que você deseja para os usuários finais se você está preparado para abstrair a infraestrutura subjacente e a processos do usuário final. No entanto, o Azure Stack inclui vários provedores de serviços de PaaS opcionais incluindo o serviço de aplicativo, bancos de dados SQL e bancos de dados MySQL. Esses são entregues como provedores de recursos, portanto, estão pronto para vários locatário, atualizado ao longo do tempo com o padrão de atualizações do Azure Stack, visível no portal do Azure Stack e bem integrado com o Azure Stack.

Além de provedores de recursos descritos acima, há serviços de PaaS adicionais disponíveis e testados como [soluções com base no modelo do Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) que são executados no IaaS, mas você, como um pode de operador do Azure Stack oferecê-las como Serviços de PaaS para seus usuários incluindo:

- Service Fabric
- Serviço de contêiner do Kubernetes
- O Hub IoT e Hub de eventos
- Etherium Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Exemplos de casos de uso para o Azure Stack:

- Modelagem financeira
- Dados clínicos e declarações
- Análise de dispositivo de IoT
- Otimização de classificação de varejo
- Otimização de cadeia de fornecimento
- IoT industrial
- Manutenção preditiva
- Cidade inteligente
- Engajamento dos cidadãos

Saiba mais sobre o Azure Stack no [o que é o Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Soluções de pilha HCI do Azure permitem que você execute máquinas virtuais no local e conectar-se facilmente ao Azure com uma solução de infraestrutura (HCI) hiperconvergente. Crie e execute aplicativos usando serviços locais consistentes do Azure para atender aos requisitos técnicos regulatórios. Executando aplicativos virtualizados no local, além de HCI de pilha do Azure permite que você substitua e consolidar a infraestrutura de classificação por vencimento do servidor e se conectar ao Azure para serviços de nuvem usando o Windows Admin Center.

HCI de pilha do Azure fornece soluções HCI validadas da plataforma Hyper-V e espaços de armazenamento diretos com o Windows Server 2019 definido por software SDDC (Datacenter). O Windows Admin Center é usado para gerenciamento e acesso integrado aos serviços do Azure, como:

- Serviço de Backup do Azure
- Azure Site Recovery
- Atualização e o azure Monitor

Para uma lista atualizada do Azure dos serviços que você pode se conectar do Azure Stack HCI para ver [conectando o Windows Server para os serviços híbridos do Azure](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Exemplos de casos de uso para o Azure Stack HCI
- Sistemas de escritório remoto ou filial
- Consolidação de data center
- Infraestrutura de área de trabalho virtual
- Infraestrutura crítica de negócios
- Armazenamento de baixo custo
- Alta disponibilidade e recuperação de desastre na nuvem
- Aplicativos empresariais como o SQL Server

Visite o [site do Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci/) exibir 70 das soluções de HCI de pilha do Azure atualmente disponíveis de parceiros da Microsoft.

## <a name="next-steps"></a>Próximas etapas

[Noções básicas de administração do Azure Stack](azure-stack-manage-basics.md)

[Início rápido: usar o portal de administração do Azure Stack](azure-stack-manage-portals.md)
