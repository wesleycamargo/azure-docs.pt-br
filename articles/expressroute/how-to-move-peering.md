---
title: "Mover um emparelhamento público no Azure ExpressRoute para o emparelhamento da Microsoft | Microsoft Docs"
description: "Este artigo mostra as etapas para mover o emparelhamento público para o emparelhamento da Microsoft no ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/09/2017
ms.author: cherylmc
ms.openlocfilehash: 4fd0f1cbba36957ae5ecc1b7436d1f1734a3ef79
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um emparelhamento público para o emparelhamento da Microsoft

O ExpressRoute dá suporte a serviços de PaaS do Azure, como o armazenamento do Azure e o Banco de Dados SQL do Azure, usando o emparelhamento da Microsoft com filtros de rota. Agora, você precisa apenas de um domínio de roteamento para acessar os serviços de PaaS e SaaS da Microsoft. Aproveite os filtros de rota para anunciar seletivamente os prefixos do serviço de PaaS para as regiões do Azure que você deseja consumir.

Este artigo ajuda você a mover uma configuração de emparelhamento público para o emparelhamento da Microsoft sem tempo de inatividade. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Você deve ter o complemento premium do ExpressRoute para usar o emparelhamento da Microsoft. Para obter mais informações sobre o complemento premium, consulte as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before"></a>Antes de começar

* Para se conectar ao emparelhamento da Microsoft, você precisa configurar e gerenciar o NAT. Seu provedor de conectividade pode configurar e gerenciar o NAT como um serviço gerenciado. Caso você pretenda acessar os serviços de PaaS e SaaS do Azure no emparelhamento do Microsoft, é importante dimensionar o pool IP do NAT corretamente. Para obter mais informações sobre o NAT para o ExpressRoute, consulte os [Requisitos do NAT para o emparelhamento da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Se você estiver usando o emparelhamento público e tiver regras de rede IP para endereços IP públicos que são usados para acessar [armazenamento do Azure](../storage/common/storage-network-security.md) ou [banco de dados do SQL Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), você precisa certificar-se de que o pool de IP de NAT configurado com o emparelhamento Microsoft está incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou a conta do SQL Azure.

* Para fazer uma movimentação para o emparelhamento da Microsoft sem tempo de inatividade, use as etapas deste artigo na ordem em que são apresentadas.

## <a name="create"></a>1. Criar um emparelhamento da Microsoft

Se um emparelhamento da Microsoft não tiver sido criado, use um dos artigos a seguir para criar um emparelhamento da Microsoft. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, solicite a ele a habilitação do emparelhamento da Microsoft para seu circuito.

  * [Criar um emparelhamento da Microsoft usando o portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Criar um emparelhamento da Microsoft usando o Azure PowerShell](expressroute-howto-routing-arm.md#msft)
  * [Criar um emparelhamento da Microsoft usando a CLI do Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Validar se o emparelhamento da Microsoft está habilitado

Verifique se o emparelhamento da Microsoft está habilitado e se os prefixos públicos anunciados estão no estado configurado.

  * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [PowerShell do Azure](expressroute-howto-routing-arm.md#getmsft)
  * [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurar e anexar um filtro de rota ao circuito

Por padrão, os novos emparelhamentos da Microsoft não anunciam nenhum prefixo até que um filtro de rota seja anexado ao circuito. Ao criar uma regra de filtro de rota, especifique a lista de comunidades de serviço para as regiões do Azure que você deseja consumir nos serviços de PaaS do Azure, conforme mostrado na seguinte captura de tela:

![Mesclar um emparelhamento público](.\media\how-to-move-peering\public.png)

Use um dos artigos a seguir para configurar filtros de rota:

  * [Configurar filtros de rota para o emparelhamento da Microsoft usando o portal do Azure](how-to-routefilter-portal.md)
  * [Configurar filtros de rota para o emparelhamento da Microsoft usando o Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurar filtros de rota para o emparelhamento da Microsoft usando a CLI do Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Excluir o emparelhamento público

Depois de verificar se o emparelhamento da Microsoft está configurado e se os prefixos que você deseja consumir estão corretamente anunciados no emparelhamento da Microsoft, você poderá excluir o emparelhamento público. Para excluir o emparelhamento público, use um dos seguintes artigos:

  * [Excluir o emparelhamento público do Azure usando o portal do Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Excluir o emparelhamento público do Azure usando o Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Excluir o emparelhamento público do Azure usando a CLI](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).