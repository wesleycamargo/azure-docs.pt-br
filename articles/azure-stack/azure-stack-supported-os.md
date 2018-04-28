---
title: Sistemas operacionais convidados suportados para a pilha do Azure | Microsoft Docs
description: Esses sistemas operacionais de convidado pode ser usados na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: ff3aea4e449e3d489b0c0f01345ecd9773c7d885
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operacionais de convidados com suporte na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="windows"></a>Windows
Pilha do Azure oferece suporte os sistemas operacionais convidados de Windows que estão listados na tabela a seguir: imagens no mercado estão disponíveis para download para a pilha do Azure. Imagens de cliente do Windows não estão disponíveis no mercado.

Durante a implantação, o Azure pilha injeta uma versão adequada do agente convidado na imagem.

| Sistema operacional | DESCRIÇÃO | Disponível no Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, versão 1709 | 64 bits | Core com contêineres |
| Windows Server 2016 | 64 bits |  Datacenter, Datacenter Core, Datacenter com contêineres |
| Windows Server 2012 R2 | 64 bits |  Datacenter |
| Windows Server 2012 | 64 bits |  Datacenter |
| Windows Server 2008 R2 SP1 | 64 bits |  Datacenter |
| Windows Server 2008 SP2 | 64 bits |  Traga sua própria imagem |
| Windows 10 *(veja a Observação 1)* | 64 bits, Pro e Enterprise | Traga sua própria imagem |

***Observação 1:****para implantar sistemas de operacionais de cliente do Windows 10 na pilha do Azure, você deve ter [Windows licenciamento por usuário](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) ou de compra por meio de um Hoster multilocatário qualificado ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Imagens do Marketplace estão disponíveis para pagamento como você-uso ou BYOL (EA/SPLA) de licenciamento. Não há suporte para o uso de ambos em uma única instância de pilha do Azure. 

Somente o Datacenter Edition está disponível no mercado; os clientes podem colocar suas próprias imagens de servidor, incluindo outras edições.

## <a name="linux"></a>Linux

Distribuições do Linux listadas aqui incluem o necessário Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Imagens personalizadas devem ser criadas com a versão mais recente de WALA pública. Versões anteriores à 2.2.18 podem não funcionar corretamente na pilha do Azure.  
>
> [nuvem init](https://cloud-init.io/) não tem suporte na pilha do Azure no momento.

| Distribuição | DESCRIÇÃO | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Contêiner Linux |  64 bits | CoreOS | Estável |
| Com base em centOS 6,9 | 64 bits | Rogue Wave | Sim |
| Com base em centOS 7.4 | 64 bits | Rogue Wave | Sim |
| ClearLinux | 64 bits | ClearLinux.org | Sim |
| Debian 8 "Jessie" | 64 bits | credativ |  Sim |
| Debian 9 "estender" | 64 bits | credativ | Sim |
| Red Hat Enterprise Linux 7. x (pendente) | 64 bits | Red Hat |Traga sua própria imagem |
| SLES 11SP4 | 64 bits | SUSE | Sim |
| SLES 12SP3 | 64 bits | SUSE | Sim |
| Ubuntu 14.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 16.04-LTS | 64 bits | Canônico | Sim |

Outras distribuições de Linux podem ter suporte no futuro.
