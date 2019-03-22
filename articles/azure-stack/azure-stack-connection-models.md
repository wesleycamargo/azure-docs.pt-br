---
title: Modelos de conexão de sistemas integrados do Azure Stack | Microsoft Docs
description: Determine decisões de planejamento para vários nós do Azure Stack de implantação.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: f58d4e4f8b0e095fe64489fb42f78ef82c5af359
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407809"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modelos de conexão de sistemas integrados do Azure Stack
Se você estiver interessado em adquirir um sistema integrado do Azure Stack, você precisará entender [várias considerações de integração do datacenter](azure-stack-datacenter-integration.md) para implantação do Azure Stack determinar como o sistema se ajustarem ao seu datacenter. Além disso, você precisará decidir como você irá integrar o Azure Stack em seu ambiente de nuvem híbrida. Este artigo fornece uma visão geral dessas decisões importantes, incluindo a conexão do Azure, o repositório de identidades e decisões do modelo de cobrança.

Se você decidir comprar um sistema integrado, o fornecedor de hardware do fabricante original do equipamento (OEM) ajuda a orientar você por meio da maior parte do processo de planejamento mais detalhadamente. Eles também executará a implantação real.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Escolha um modelo de conexão de implantação do Azure Stack
Você pode optar por implantar a pilha do Azure conectada à internet (e para o Azure) ou desconectado. Para tirar o máximo proveito da pilha do Azure, incluindo cenários híbridos entre o Azure Stack e o Azure, você pode querer implantar conectado ao Azure. Essa opção define quais opções estão disponíveis para seu armazenamento de identidade (Azure Active Directory ou serviços de Federação do Active Directory) e o modelo de cobrança (como você com base no uso de pagamento cobrança ou baseada em capacidade cobrança) conforme resumido no diagrama e tabela a seguir: 

![Implantação de pilha e cenários de cobrança do Azure](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Esse é um ponto de decisão fundamental! Escolher os serviços de Federação do Active Directory (AD FS) ou Azure Active Directory (Azure AD) é uma decisão única que você precisa fazer no momento da implantação. Você não pode alterar isso posteriormente sem reimplantar todo o sistema.  


|Opções|Conectado ao Azure|Desconectado do Azure|
|-----|:-----:|:-----:|
|AD do Azure|![Com suporte](media/azure-stack-connection-models/check.png)| |
|AD FS|![Com suporte](media/azure-stack-connection-models/check.png)|![Com suporte](media/azure-stack-connection-models/check.png)|
|Cobrança com base no consumo|![Com suporte](media/azure-stack-connection-models/check.png)| |
|Cobrança baseada em capacidade|![Com suporte](media/azure-stack-connection-models/check.png)|![Com suporte](media/azure-stack-connection-models/check.png)|
|Licenciamento| Contrato Enterprise ou provedor de soluções de nuvem | Contrato Enterprise |
|Patches e atualizações|Pacote de atualização pode ser baixado diretamente da Internet para o Azure Stack |  Obrigatório<br><br>Também requer uma mídia removível<br> e um dispositivo conectado separado |
| Registro | Automatizado | Obrigatório<br><br>Também requer uma mídia removível<br> e um dispositivo conectado separado |

Depois de decidir sobre o modelo de conexão do Azure a ser usado para implantação do Azure Stack, decisões adicionais e dependente de conexão devem ser feitas para o repositório de identidades e o método de cobrança. 

## <a name="next-steps"></a>Próximas etapas

[Decisões de implantação do Azure Stack conectadas do Azure](azure-stack-connected-deployment.md)

[Decisões de implantação do Azure Stack desconectadas do Azure](azure-stack-disconnected-deployment.md)
