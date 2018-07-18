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
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 8d9337053c8905886ed4429d64f8ef5b4e2c7d14
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060440"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operacionais de convidados com suporte na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="windows"></a>Windows

A pilha do Azure oferece suporte os sistemas de operacionais de convidados do Windows listados na tabela a seguir:

| Sistema operacional | DESCRIÇÃO | Disponível no Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, versão 1709 | 64 bits | Core com contêineres |
| Windows Server 2016 | 64 bits |  Datacenter, Datacenter Core, Datacenter com contêineres |
| Windows Server 2012 R2 | 64 bits |  Datacenter |
| Windows Server 2012 | 64 bits |  Datacenter |
| Windows Server 2008 R2 SP1 | 64 bits |  Datacenter |
| Windows Server 2008 SP2 | 64 bits |  Traga sua própria imagem |
| Windows 10 *(veja a Observação 1)* | 64 bits, Pro e Enterprise | Traga sua própria imagem |

***Observação 1:*** *para implantar sistemas de operacionais de cliente do Windows 10 na pilha do Azure, você deve ter [Windows licenciamento por usuário](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) ou de compra por meio de um Hoster multilocatário qualificado ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

Imagens do Marketplace estão disponíveis para pagamento como você-uso ou BYOL (EA/SPLA) de licenciamento. Não há suporte para uso em uma única instância de pilha do Azure. Durante a implantação, o Azure pilha injeta uma versão adequada do agente convidado na imagem.

 Edições Datacenter estão disponíveis no mercado para download; os clientes podem colocar suas próprias imagens de servidor, incluindo outras edições. Imagens de cliente do Windows não estão disponíveis no mercado.

## <a name="linux"></a>Linux

Distribuições do Linux listadas como disponíveis no mercado incluem o necessário Windows Azure Linux Agent (WALA). Se você colocar sua própria imagem para a pilha do Azure, siga as diretrizes [Linux adicionar imagens à pilha Azure](azure-stack-linux.md).

> [!NOTE]
> Imagens personalizadas devem ser criadas com a versão mais recente de WALA pública. Versões anteriores à 2.2.18 podem não funcionar corretamente na pilha do Azure.
>
> [nuvem init](https://cloud-init.io/) não tem suporte na pilha do Azure no momento.

| Distribuição | DESCRIÇÃO | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Com base em centOS 6,9 | 64 bits | Rogue Wave | Sim |
| Com base em centOS 7.4 | 64 bits | Rogue Wave | Sim |
| ClearLinux | 64 bits | ClearLinux.org | Sim |
| Contêiner Linux |  64 bits | CoreOS | Estável |
| Debian 8 "Jessie" | 64 bits | credativ |  Sim |
| Debian 9 "estender" | 64 bits | credativ | Sim |
| Red Hat Enterprise Linux 7. x | 64 bits | Red Hat |Traga sua própria imagem |
| SLES 11SP4 | 64 bits | SUSE | Sim |
| SLES 12SP3 | 64 bits | SUSE | Sim |
| Ubuntu 14.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 16.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 18.04-LTS | 64 bits | Canônico | Sim |

Outras distribuições de Linux podem ter suporte no futuro.

Para obter informações de suporte do Red Hat Enterprise Linux, consulte [Red Hat e o Azure pilha: perguntas frequentes sobre](https://access.redhat.com/articles/3413531).
