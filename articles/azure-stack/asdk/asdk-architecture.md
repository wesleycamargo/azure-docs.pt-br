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
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c69b124f84e87e8f0b937dfa275378c376894f9b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447193"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitetura do Kit de desenvolvimento do Microsoft Azure Stack
O Azure Stack desenvolvimento ASDK (Kit) é uma implantação de nó único do Azure Stack em execução em um único computador host. Componentes de roteamento de borda é instalado no computador host para fornecer recursos de NAT e VPN para o Azure Stack. As funções de infraestrutura do Azure Stack é executado na camada de computador físico do host do Hyper-V.


## <a name="virtual-machine-roles"></a>Funções de máquina virtual
O ASDK oferece serviços usando as seguintes VMs hospedadas no computador de host do kit de desenvolvimento:

| NOME | DESCRIÇÃO |
| ----- | ----- |
| **AzS-ACS01** | Serviços de armazenamento do Azure Stack.|
| **AzS-ADFS01** | Serviços de Federação do Active Directory (ADFS).  |
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
