---
title: Arquitetura do Kit de desenvolvimento do Azure Stack | Microsoft Docs
description: Descreve a arquitetura do Kit de desenvolvimento na pilha do Azure (ASDK).
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 2cee4d75a111f56012782228d8070bb728720aca
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238323"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitetura do Kit de desenvolvimento do Microsoft Azure Stack
O Azure Stack desenvolvimento ASDK (Kit) é uma implantação de nó único do Azure Stack. Todos os componentes são instalados em máquinas virtuais em execução em um único computador host. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitetura lógica
O diagrama a seguir ilustra a arquitetura lógica do ASDK e seus componentes.

![Arquitetura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Funções de máquina virtual
O ASDK oferece serviços usando as seguintes VMs hospedadas no computador de host do kit de desenvolvimento:

| NOME | DESCRIÇÃO |
| ----- | ----- |
| **AzS-ACS01** | Serviços de armazenamento do Azure Stack.|
| **AzS-ADFS01** | Serviços de Federação do Active Directory (ADFS).  |
| **AzS-BGPNAT01** | Roteador de borda e fornece recursos de NAT e VPN para o Azure Stack. |
| **AzS-CA01** | Serviços de autoridade de certificado para serviços de função do Azure Stack.|
| **AzS-DC01** | Serviços de diretório, DNS e DHCP Active Directory para o Microsoft Azure Stack.|
| **AzS-ERCS01** | Console de recuperação de emergência VM. |
| **AzS-GWY01** | Serviços de gateway de borda, como as conexões VPN de site a site para redes de locatário.|
| **AzS-NC01** | Controlador de rede, que gerencia os serviços de rede do Azure Stack.  |
| **AzS-SLB01** | O balanceamento de carga multiplexador serviços no Azure Stack para locatários e serviços de infraestrutura do Azure Stack.  |
| **AzS-SQL01** | Repositório de dados internos para funções de infraestrutura do Azure Stack.  |
| **AzS-WAS01** | Portal administrativo de pilha do Azure e serviços do Azure Resource Manager.|
| **AzS-WASP01**| Portal do usuário (Locatário) da pilha do Azure e serviços do Azure Resource Manager.|
| **AzS-XRP01** | Controlador de gerenciamento de infra-estrutura para Microsoft Azure Stack, incluindo os provedores de recursos de computação, rede e armazenamento.|


## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre tarefas básicas de administração ASDK](asdk-admin-basics.md)
