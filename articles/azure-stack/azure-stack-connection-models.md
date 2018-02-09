---
title: "Modelos de conexão de sistemas de integrados de pilha do Azure | Microsoft Docs"
description: "Determine as decisões de vários nós do Azure pilha de planejamento de implantação."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e6c94ef1172ea6380a94d5907c24069ed8c48ff5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modelos de conexão de sistemas do Azure pilha integrado
Se você estiver interessado em um sistema de pilha do Azure integrado, você precisará compreender [várias considerações de integração do Data Center](azure-stack-datacenter-integration.md) para implantação de pilha do Azure determinar como o sistema irá se ajustam ao seu datacenter. Além disso, você precisará decidir exatamente como você integrará pilha do Azure em seu ambiente de nuvem híbrida. Este artigo fornece uma visão geral dessas decisões principais, inclusive conexão do Azure, o repositório de identidades e decisões de modelo de cobrança.

Se você decidir comprar um sistema integrado, o fornecedor do hardware fabricante (OEM) ajuda a guiá-lo na maior parte do processo de planejamento em mais detalhes. Eles também executará a implantação real.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Escolha um modelo de conexão de implantação de pilha do Azure
Você pode optar por implantar o Azure pilha conectada à internet (e no Azure) ou desconectado. Para obter o máximo proveito da pilha do Azure, incluindo cenários híbridos entre a pilha do Azure e o Azure, você deseja implantar conectado ao Azure. Essa opção define quais opções estão disponíveis para seu repositório de identidade (Active Directory do Azure ou os serviços de Federação do Active Directory) e o modelo de cobrança (de pagamento que você use baseado em com capacidade ou cobrança cobrança) como resumido o diagrama e a tabela a seguir: 

![Implantação de pilha e cenários de cobrança do Azure](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Esse é um ponto de decisão chave! Escolha os serviços de Federação do Active Directory (AD FS) ou o Azure Active Directory (AD do Azure) é uma decisão única que você deve fazer no momento da implantação. Você não pode alterar isso posteriormente sem reimplantar o sistema inteiro.  


|Opções|Conectado ao Azure|Desconectado do Azure|
|-----|-----|-----|
|AD do Azure|![Com suporte](media/azure-stack-connection-models/check.png)| |
|AD FS|![Com suporte](media/azure-stack-connection-models/check.png)|![Com suporte](media/azure-stack-connection-models/check.png)|
|Cobrança com base no consumo|![Com suporte](media/azure-stack-connection-models/check.png)| |
|Baseado na capacidade de cobrança|![Com suporte](media/azure-stack-connection-models/check.png)|![Com suporte](media/azure-stack-connection-models/check.png)|
|Baixe os pacotes de atualização diretamente para a pilha do Azure|![Com suporte](media/azure-stack-connection-models/check.png)|  |

Depois que você decidir no modelo de conexão do Azure a ser usado para implantação de pilha do Azure, decisões dependente de conexão adicionais devem ser feitas para o armazenamento de identidade e o método de cobrança. 

## <a name="next-steps"></a>Próximas etapas

[Azure conectadas decisões de implantação de pilha do Azure](azure-stack-connected-deployment.md)

[Decisões de implantação do Azure pilha desconectadas do Azure](azure-stack-disconnected-deployment.md)
