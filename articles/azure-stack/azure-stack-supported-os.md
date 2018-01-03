---
title: Suporte de sistema operacional convidado para a pilha do Azure | Microsoft Docs
description: Esses sistemas operacionais de convidado pode ser usados na pilha do Azure.
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGoldner
ms.openlocfilehash: af524b5f40fb79648d91deb54186826902aa8cd2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operacionais de convidados com suporte na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="windows"></a>Windows
A pilha do Azure suporta os seguintes sistemas operacionais de convidados Windows. Imagens no mercado estão disponíveis para download para a pilha do Azure. Imagens de cliente do Windows não estão disponíveis no mercado.

Durante a implantação, a pilha do Azure garantirá que uma versão adequada do agente convidado é injetada a imagem.

| Sistema operacional | DESCRIÇÃO | Publicador | Tipo de sistema operacional | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 bits | Microsoft | Windows | Datacenter |
| Windows Server 2012 | 64 bits | Microsoft | Windows | Datacenter |
| Windows Server 2012 R2 | 64 bits | Microsoft | Windows | Datacenter |
| Windows Server 2016 | 64 bits | Microsoft | Windows | Datacenter, Datacenter Core, Datacenter com contêineres |
| Windows 7 | 64 bits, Pro e Enterprise | Microsoft | Windows | Não  |
| Windows 8.1 | 64 bits, Pro e Enterprise | Microsoft | Windows | Não  |
| Windows 10 | 64 bits, Pro e Enterprise | Microsoft | Windows | Não  |

## <a name="linux"></a>Linux

Distribuições do Linux listadas aqui incluem o necessário Windows Azure Linux Agent (WALA). 

> [!NOTE]
> Imagens criadas com as versões WALA mais antigas que o 2.2.3 *não* com suporte e provavelmente não implantar.

| Distribuição | DESCRIÇÃO | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Contêiner Linux |  64 bits | CoreOS | Estável |
| Com base em centOS 6,9 | 64 bits | Rogue Wave | Sim |
| CentOS 7.3 | 64 bits | Rogue Wave | Sim |
| Com base em centOS 7.4 | 64 bits | Rogue Wave | Sim |
| Debian 8 "Jessie" | 64 bits | credativ |  Sim |
| Debian 9 "estender" | 64 bits | credativ | Sim |
| Oracle Linux | 64 bits | Oracle | Não  |
| Red Hat Enterprise Linux 7. x | 64 bits | Red Hat | Não  |
| SLES 11SP4 | 64 bits | SUSE | Sim |
| SLES 12SP3 | 64 bits | SUSE | Sim |
| Ubuntu 14.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 16.04-LTS | 64 bits | Canônico | Sim |

Outras distribuições de Linux podem ter suporte no futuro.




