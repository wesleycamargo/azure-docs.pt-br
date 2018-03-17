---
title: "Uma introdução para o Kit de desenvolvimento do Azure pilha (ASDK) | Microsoft Docs"
description: "Descreve o que é o ASDK e casos de uso comuns para avaliar a pilha do Microsoft Azure."
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
ms.topic: overview
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5a268a29c7a767084049bf56270aa8bc9d2ccc3f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="what-is-the-azure-stack-development-kit"></a>O que é o Kit de desenvolvimento de pilha do Azure?
[Sistemas integrados de pilha do Microsoft Azure](.\.\azure-stack-poc.md) variam em tamanho de 4-12 nós e têm suporte em conjunto por um parceiro de hardware e a Microsoft. Use sistemas de pilha do Azure integradas para ativar novos cenários para suas cargas de trabalho de produção. Se você tiver um operador de pilha do Azure que gerencia a infraestrutura de sistemas integrados e ofertas de serviços, consulte nosso [documentação operador](https://docs.microsoft.com/azure/azure-stack).

O Kit de desenvolvimento de pilha do Azure (ASDK) é uma implantação de nó único da pilha do Azure que você pode baixar e usar **gratuitamente**. Todos os componentes ASDK são instalados em máquinas virtuais em execução em um computador do servidor de host único que deve atender ou exceder o [requisitos mínimos de hardware](asdk-deploy-considerations.md#hardware). O ASDK visa fornecer um ambiente no qual você pode avaliar a pilha do Azure e desenvolver aplicativos modernos usando APIs e ferramentas consistentes com o Azure em um *não seja de produção* ambiente. 

> [!IMPORTANT]
> O ASDK não se destina a ser usado ou tem suporte em um ambiente de produção.

Como todos os componentes ASDK são implantados em um computador de host do kit de desenvolvimento único, limitada de recursos físicos estão disponíveis. Em implantações de ASDK, a pilha do Azure VMs de infraestrutura e de locatário VMs coexistirem no mesmo computador servidor. Essa configuração não se destina a avaliação de desempenho ou escala.

O ASDK é projetada para fornecer uma experiência consistente com o Azure de nuvem híbrida para:
- **Os administradores** (operadores de pilha do Azure). O ASDK é um ótimo recurso para avaliar e saiba mais sobre os serviços disponíveis da pilha do Azure.
- **Os desenvolvedores**. O ASDK pode ser usado para desenvolver híbrido ou aplicativos modernos (ambientes de desenvolvimento/teste) local. Isso oferece a capacidade de repetição de experiência de desenvolvimento antes ou juntamente com implantações de produção de pilha do Azure. 

Assista a este vídeo curto para saber mais sobre o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Diferenças de pilha do Azure ASDK e vários nós
Implantações de ASDK de nó único diferem de implantações do Azure pilha com vários nós em alguns aspectos importantes que você deve estar atento.

|DESCRIÇÃO|ASDK|Pilha de vários nó do Azure|
|-----|-----|-----|
|**Escala**|Todos os componentes são instalados em um computador de servidor de nó único.|Podem variar em tamanho de 4-12 nós.|
|**Resiliência**|Configuração de nó único não fornece alta disponibilidade|[Alta disponibilidade](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) recursos têm suporte.|
|**Rede**|O ASDK usa uma VM denominada AzS BGPNAT01 para rotear todo o tráfego de rede ASDK. Não há requisitos adicionais de comutador.|A VM AzS BGPNAT01 não existe em implantações de vários nós. Mais complexa [infraestrutura de roteamento de rede](.\.\azure-stack-network.md#network-infrastructure) é necessário incluindo Top-Of-Rack (TOR), controlador BMC (Baseboard Management) e opções de borda (rede de datacenter).|
|**Processo de patch e atualização**|Para mover para uma nova versão do ASDK, você deve reimplantar o ASDK no computador de host do kit de desenvolvimento.|[Patch e atualização](.\.\azure-stack-updates.md) processo usado para atualizar a versão instalada de pilha do Azure.|
|**Suporte**|Fórum do MSDN do Azure pilha. Suporte de atendimento ao cliente Microsoft e suporte (CSS) é *não* disponíveis para ambientes de não produção.|[Fórum do MSDN do Azure pilha](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) e suporte a CSS completos.|
| | |

## <a name="learn-about-available-services"></a>Saiba mais sobre os serviços disponíveis
Como um operador de pilha do Azure, você precisa saber quais serviços você pode disponibilizar para os usuários. A pilha do Azure suporta um subconjunto de serviços do Azure e a lista de serviços com suporte continuarão a evoluir ao longo do tempo.

### <a name="foundational-services"></a>Serviços fundamentais
Por padrão, a pilha do Azure inclui os seguintes "fundamentais serviços" quando você implanta o ASDK:
- Computação
- Armazenamento
- Rede
- Cofre da Chave

Com esses serviços fundamentais, você pode oferecer a infraestrutura-como-um-serviço (IaaS) para os usuários com configuração mínima.

### <a name="additional-services"></a>Serviços adicionais
Atualmente, há suporte para os seguintes serviços de plataforma-como-um-serviço (PaaS) adicionais:
- Serviço de Aplicativo
- Funções do Azure
- Bancos de dados SQL e MySQL

> [!NOTE]
> Esses serviços exigem configuração adicional antes que você pode torná-los disponíveis aos usuários e não estão disponíveis por padrão quando você instala o ASDK.

## <a name="service-roadmap"></a>Mapa de serviço
Pilha do Azure continuará a adicionar suporte a serviços adicionais do Azure. Para saber mais sobre o que é o próximo passo com pilha do Azure, consulte o [roteiro da pilha do Azure](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Próximas etapas
Para começar a avaliação de pilha do Azure, você precisa preparar o host do kit de desenvolvimento de computador do servidor e, em seguida, [instalar o ASDK](asdk-deploy.md). Depois disso, você pode entrar portais de administrador e usuário para começar a usar a pilha do Azure.