---
title: Arquitetura de Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: Descreve a arquitetura do Kit de desenvolvimento na pilha do Azure (ASDK).
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
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitetura de pilha Development Kit do Microsoft Azure
O Kit de desenvolvimento de pilha do Azure (ASDK) é uma implantação de nó único da pilha do Azure. Todos os componentes são instalados em máquinas virtuais em execução em uma máquina de host único. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitetura lógica
O diagrama a seguir ilustra a arquitetura lógica do ASDK e seus componentes.

![Arquitetura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Funções de máquina virtual
O ASDK oferece serviços usando as seguintes máquinas virtuais hospedadas no computador de host do kit de desenvolvimento:

| NOME | DESCRIÇÃO |
| ----- | ----- |
| **AzS-ACS01** | Serviços de armazenamento de pilha do Azure.|
| **AzS-ADFS01** | Serviços de Federação do Active Directory (ADFS).  |
| **AzS-BGPNAT01** | Roteador de borda e fornece recursos de NAT e VPN para a pilha do Azure. |
| **AzS-CA01** | Serviços de autoridade de certificado de serviços de função de pilha do Azure.|
| **AzS-DC01** | Active Directory, DNS e DHCP dos serviços para a pilha do Microsoft Azure.|
| **AzS-ERCS01** | Console de recuperação de emergência VM. |
| **AzS-GWY01** | Serviços de gateway de borda, como conexões VPN de site a site para redes de locatário.|
| **AzS-NC01** | Controlador de rede, que gerencia os serviços de rede de pilha do Azure.  |
| **AzS-SLB01** | O balanceamento de carga multiplexador serviços na pilha do Azure para locatários e serviços de infraestrutura de pilha do Azure.  |
| **AzS-SQL01** | Repositório de dados internos para funções de infraestrutura de pilha do Azure.  |
| **AzS-WAS01** | Portal administrativo de pilha do Azure e serviços do Azure Resource Manager.|
| **AzS-WASP01**| Portal do usuário (Locatário) de pilha do Azure e serviços do Azure Resource Manager.|
| **AzS-XRP01** | Controlador de gerenciamento de infraestrutura para a pilha do Microsoft Azure, incluindo os provedores de recursos de computação, rede e armazenamento.|


## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre as tarefas básicas de administração de ASDK](asdk-admin-basics.md)
