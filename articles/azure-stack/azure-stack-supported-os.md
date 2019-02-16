---
title: Sistemas operacionais convidados suportados para o Azure Stack | Microsoft Docs
description: Esses sistemas operacionais de convidado pode ser usados no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 67022b1d00c8982d7b6e6eff77ffb648bfc557ec
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301274"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operacionais convidados com suporte no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="windows"></a> Windows

O Azure Stack oferece suporte os sistemas de operacionais de convidados do Windows listados na tabela a seguir:

| Sistema operacional | DESCRIÇÃO | Disponível no Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, versão 1709 | 64 bits | Core com contêineres |
| Windows Server 2016 | 64 bits |  Datacenter, Datacenter Core, Datacenter com contêineres |
| Windows Server 2012 R2 | 64 bits |  Datacenter |
| Windows Server 2012 | 64 bits |  Datacenter |
| Windows Server 2008 R2 SP1 | 64 bits |  Datacenter |
| Windows Server 2008 SP2 | 64 bits |  Traga sua própria imagem |
| Windows 10 *(veja a Observação 1)* | 64 bits, Pro e Enterprise | Traga sua própria imagem |

> [!NOTE]
> Para implantar sistemas de operacionais de cliente do Windows 10 no Azure Stack, você deve ter [Windows de licenciamento por usuário](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) ou de compra por meio de um Hoster multilocatário qualificado ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Imagens do Marketplace estão disponíveis para pagamento-como-o uso ou BYOL (EA/SPLA) de licenciamento. Não há suporte para uso de ambos em uma única instância do Azure Stack. Durante a implantação, o Azure Stack injeta uma versão adequada do agente convidado em imagem.

Edições Datacenter estão disponíveis no mercado para download; os clientes podem trazer suas próprias imagens de servidor, incluindo outras edições. Imagens do Windows client não estão disponíveis no Marketplace.

## <a name="linux"></a>Linux

Distribuições do Linux listadas como disponíveis no Marketplace incluem o necessário Windows Azure Linux Agent (WALA). Se você traga sua própria imagem para o Azure Stack, siga as diretrizes [Linux adicionar imagens ao Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Imagens personalizadas devem ser criadas com a versão mais recente de WALA de público ou com a versão 2.2.20. Versões mais antigas que 2.2.20 e entre 2.2.20 e 2.2.35 (exclusivo) podem não funcionar corretamente no Azure Stack. 
>
> [a inicialização de nuvem](https://cloud-init.io/) não tem suporte no Azure Stack no momento.

| Distribuição | DESCRIÇÃO | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Baseado em centOS 6.9 | 64 bits | Rogue Wave | Sim |
| Baseado em centOS 7.4 | 64 bits | Rogue Wave | Sim |
| ClearLinux | 64 bits | ClearLinux.org | Sim |
| Contêiner Linux |  64 bits | CoreOS | Estável |
| Debian 8 "Jessie" | 64 bits | credativ |  Sim |
| Debian 9 "Stretch" | 64 bits | credativ | Sim |
| Red Hat Enterprise Linux 7.x | 64 bits | Red Hat |Traga sua própria imagem |
| SLES 11SP4 | 64 bits | SUSE | Sim |
| SLES 12SP3 | 64 bits | SUSE | Sim |
| Ubuntu 14.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 16.04-LTS | 64 bits | Canônico | Sim |
| Ubuntu 18.04-LTS | 64 bits | Canônico | Sim |

Para obter informações de suporte do Red Hat Enterprise Linux, consulte [Red Hat e Azure Stack: Perguntas frequentes](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Stack Marketplace, consulte os artigos a seguir:

- [Baixar itens do Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
