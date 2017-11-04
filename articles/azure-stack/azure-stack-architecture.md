---
title: Arquitetura de pilha Development Kit do Microsoft Azure | Microsoft Docs
description: Exiba a arquitetura do Kit de desenvolvimento de pilha do Microsoft Azure.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e8ff17e069bd9237bf06cd79b4c222c50a999eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitetura de pilha Development Kit do Microsoft Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

O Kit de desenvolvimento de pilha do Azure é uma implantação de nó único da pilha do Azure. Todos os componentes são instalados em máquinas virtuais em execução em uma máquina de host único. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitetura lógica
O diagrama a seguir ilustra a arquitetura lógica do kit de desenvolvimento da pilha do Azure e seus componentes.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Funções de máquina virtual
O kit de desenvolvimento de pilha do Azure oferece serviços usando as seguintes máquinas virtuais no host:

| Nome | Descrição |
| ----- | ----- |
| **AzS ACS01** | Serviços de armazenamento de pilha do Azure.|
| **AzS ADFS01** | Serviços de Federação do Active Directory (ADFS).  |
| **AzS BGPNAT01** | Roteador de borda e fornece recursos de NAT e VPN para a pilha do Azure. |
| **AzS CA01** | Serviços de autoridade de certificado de serviços de função de pilha do Azure.|
| **AzS-DC01.** | Active Directory, DNS e DHCP dos serviços para a pilha do Microsoft Azure.|
| **AzS ERCS01** | Console de recuperação de emergência VM. |
| **AzS GWY01** | Serviços de gateway de borda, como conexões VPN de site a site para redes de locatário.|
| **AzS NC01** | Controlador de rede, que gerencia os serviços de rede de pilha do Azure.  |
| **AzS SLB01** | O balanceamento de carga multiplexador serviços na pilha do Azure para locatários e serviços de infraestrutura de pilha do Azure.  |
| **AzS SQL01** | Repositório de dados internos para funções de infraestrutura de pilha do Azure.  |
| **AzS WAS01** | Portal administrativo de pilha do Azure e serviços do Azure Resource Manager.|
| **AzS WASP01**| Portal do usuário (Locatário) de pilha do Azure e serviços do Azure Resource Manager.|
| **AzS XRP01** | Controlador de gerenciamento de infraestrutura para a pilha do Microsoft Azure, incluindo os provedores de recursos de computação, rede e armazenamento.|


## <a name="next-steps"></a>Próximas etapas
[Implantar a pilha do Azure](azure-stack-deploy.md)

[Primeiros cenários para experimentar](azure-stack-first-scenarios.md)

