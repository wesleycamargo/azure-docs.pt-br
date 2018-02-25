---
title: Sistemas operacionais convidados suportados para a pilha do Azure | Microsoft Docs
description: Esses sistemas operacionais de convidado pode ser usados na pilha do Azure.
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operacionais de convidados com suporte na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="windows"></a>Windows
Pilha do Azure oferece suporte os sistemas operacionais convidados de Windows que estão listados na tabela a seguir: imagens no mercado estão disponíveis para download para a pilha do Azure. Imagens de cliente do Windows não estão disponíveis no mercado.

Durante a implantação, o Azure pilha injeta uma versão adequada do agente convidado na imagem.

| Sistema operacional | DESCRIÇÃO | Publicador | Tipo de sistema operacional | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 bits | Microsoft | Windows | Datacenter |
| Windows Server 2012 | 64 bits | Microsoft | Windows | Datacenter |
| Windows Server 2012 R2 | 64 bits | Microsoft | Windows | Datacenter |
| Windows Server 2016 | 64 bits | Microsoft | Windows | Datacenter, Datacenter Core, Datacenter com contêineres |
| Windows 10 *(veja a Observação 1)* | 64 bits, Pro e Enterprise | Microsoft | Windows | Não  |

***Observação 1:****para implantar sistemas de operacionais de cliente do Windows 10 na pilha do Azure, você deve ter [Windows licenciamento por usuário](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) ou de compra por meio de um Hoster multilocatário qualificado ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).* 


## <a name="linux"></a>Linux

Distribuições do Linux listadas aqui incluem o necessário Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Imagens criadas com as versões WALA mais antigas que o 2.2.3 *não* com suporte e provavelmente não implantar. Algumas versões do agente WALA são conhecidos não funcionar em VMs de pilha do Azure, incluindo versões 2.2.12 e 2.2.13.
>
> [nuvem init](https://cloud-init.io/) só tem suporte no Ubuntu distribuições na pilha do Azure.

| Distribuição | DESCRIÇÃO | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Contêiner Linux |  64 bits | CoreOS | Estável |
| Com base em centOS 6,9 | 64 bits | Rogue Wave | Sim |
| Com base em centOS 7.4 | 64 bits | Rogue Wave | Sim |
| Debian 8 "Jessie" | 64 bits | credativ |  Sim |
| Debian 9 "estender" | 64 bits | credativ | Sim |
| Red Hat Enterprise Linux 7. x (pendente) | 64 bits | Red Hat | Não  |
| SLES 11SP4 | 64 bits | SUSE | Sim |
| SLES 12SP3 | 64 bits | SUSE | Sim |
| Ubuntu 14.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 16.04-LTS | 64 bits | Canônico | Sim |

Outras distribuições de Linux podem ter suporte no futuro.
