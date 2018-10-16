---
title: Uma introdução para o Azure Stack desenvolvimento ASDK (Kit) | Microsoft Docs
description: Descreve o que é o ASDK e casos de uso comuns para avaliar o Microsoft Azure Stack.
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
ms.topic: overview
ms.custom: mvc
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fa20f746e55f784e02244355c96ac273b9906acc
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339535"
---
# <a name="what-is-the-azure-stack-development-kit"></a>O que é o Kit de desenvolvimento do Azure Stack?
[Sistemas integrados do Microsoft Azure Stack](.\.\azure-stack-poc.md) variam de tamanho de 4 a 12 nós e são compatíveis em conjunto com um parceiro de hardware e a Microsoft. Use sistemas integrados do Azure Stack para habilitar novos cenários para suas cargas de trabalho de produção. Se você for um operador do Azure Stack que gerencia a infra-estrutura de sistemas integrados e oferece serviços, consulte nosso [documentação do operador](https://docs.microsoft.com/azure/azure-stack).

O Azure Stack desenvolvimento ASDK (Kit) é uma implantação de nó único do Azure Stack que você pode baixar e usar **gratuitamente**. Todos os componentes ASDK são instalados em máquinas virtuais em execução em um computador do servidor de host único que deve atender ou exceder a [requisitos mínimos de hardware](asdk-deploy-considerations.md#hardware). O ASDK visa fornecer um ambiente no qual você pode avaliar o Azure Stack e desenvolver aplicativos modernos usando APIs e ferramentas consistentes com o Azure em uma *não seja de produção* ambiente. 

> [!IMPORTANT]
> O ASDK não se destina a ser usado ou tem suporte em um ambiente de produção.

Como todos os componentes ASDK são implantados em um computador de host do kit de desenvolvimento, há recursos físicos limitados disponíveis. Com implantações de ASDK, as VMs de infraestrutura do Azure Stack e o locatário VMs coexistam no mesmo computador servidor. Essa configuração não se destina a avaliação de dimensionamento ou desempenho.

O ASDK foi projetado para fornecer uma experiência de nuvem híbrida consistente do Azure para:
- **Os administradores** (operadores de pilha do Azure). O ASDK é um ótimo recurso para avaliar e saber mais sobre os serviços do Azure Stack disponíveis.
- **Os desenvolvedores**. O ASDK pode ser usado para desenvolver híbrida ou modernos aplicativos localmente (ambientes de desenvolvimento/teste). Isso oferece a capacidade de repetição da experiência de desenvolvimento antes ou juntamente com implantações de produção do Azure Stack. 

Assista a este breve vídeo para saber mais sobre o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Diferenças do Azure Stack ASDK e vários nós
Implantações de ASDK de nó único diferem de implantações do Azure Stack com vários nós em alguns aspectos importantes que você deve estar atento.

|DESCRIÇÃO|ASDK|Vários nó do Azure Stack|
|-----|-----|-----|
|**Escala**|Todos os componentes são instalados em um computador do servidor de nó único.|Pode variar em tamanho de 4 a 12 nós.|
|**Resiliência**|Configuração de nó único não oferece alta disponibilidade|[Obter alta disponibilidade](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) recursos têm suporte.|
|**Rede**|O ASDK usa uma VM denominada AzS-BGPNAT01 para rotear todo o tráfego de rede ASDK. Não há requisitos adicionais de comutador.|A VM AzS-BGPNAT01 não existe em implantações de vários nós. Mais complexos [infraestrutura de roteamento de rede](.\.\azure-stack-network.md#network-infrastructure) é necessário incluindo Top-Of-Rack (TOR), Baseboard Management Controller (BMC) e comutadores de borda (rede de datacenter).|
|**Processo de patch e atualização**|Para mover para uma nova versão do ASDK, você deve reimplantar o ASDK no computador de host do kit de desenvolvimento.|[Aplicar patch e atualização](.\.\azure-stack-updates.md) processo usado para atualizar a versão instalada do Azure Stack.|
|**Suporte**|Fórum do MSDN do Azure Stack. Suporte de atendimento ao cliente Microsoft e suporte (CSS) está *não* disponíveis para ambientes de não produção.|[Fórum do MSDN do Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) e dar suporte a CSS completas.|
| | |

## <a name="learn-about-available-services"></a>Saiba mais sobre os serviços disponíveis
Como um operador de pilha do Azure, você precisa saber quais serviços você pode disponibilizar aos seus usuários. O Azure Stack oferece suporte a um subconjunto de serviços do Azure e a lista de serviços com suporte continuará a evoluir ao longo do tempo.

### <a name="foundational-services"></a>Serviços fundamentais
Por padrão, o Azure Stack inclui os seguintes "serviços fundamentais" quando você implanta o ASDK:
- Computação
- Armazenamento
- Rede
- Key Vault

Com esses serviços fundamentais, você pode oferecer infraestrutura-como um serviço (IaaS) para os usuários com configuração mínima.

### <a name="additional-services"></a>Serviços adicionais
Atualmente, há suporte para os seguintes serviços de plataforma-como um serviço (PaaS) adicionais:
- Serviço de Aplicativo
- Funções do Azure
- Bancos de dados SQL e MySQL

> [!NOTE]
> Esses serviços exigem configuração adicional antes que você pode torná-los disponíveis aos usuários e não estão disponíveis por padrão quando você instala o ASDK.

## <a name="service-roadmap"></a>Roteiro de serviço
O Azure Stack continuará a adicionar suporte para serviços adicionais do Azure. Para saber mais sobre o que está por vir com o Azure Stack, consulte o [roteiro do Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Próximas etapas
Para começar a avaliar o Azure Stack, primeiro você precisa [baixar a mais recente ASDK](asdk-download.md) e preparar o computador de host ASDK. Depois de preparar o host do kit de desenvolvimento, você pode instalar o ASDK e entrar para os portais de administrador e usuário para começar a usar o Azure Stack.