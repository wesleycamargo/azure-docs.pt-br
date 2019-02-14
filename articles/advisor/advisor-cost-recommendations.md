---
title: Reduza os custos de serviço usando Assistente do Azure | Microsoft Docs
description: Use o Azure Advisor para otimizar o custo de suas implantações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: c76c7bdb398184cc297831c9395063e7bf0f6bdc
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492531"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduza os custos de serviço usando o Assistente do Azure

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **Custo** no painel do Assistente.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando ou desligando instâncias subutilizadas 

Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. O Assistente monitora o uso da máquina virtual durante 14 dias e, depois, identifica as máquinas virtuais com baixa utilização. As máquinas virtuais cuja utilização da CPU é de 5% ou menos e cujo uso de rede é de 7 MB ou menos durante quatro ou mais dias são consideradas máquinas virtuais de baixa utilização.

O Assistente mostra o custo estimado da continuação da execução da máquina virtual, de forma que você possa optar por desligá-la ou redimensioná-la.

Se você deseja ser mais agressivo na identificação de máquinas virtuais subutilizadas, você pode ajustar a regra de média de utilização da CPU para cálculo por assinatura.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos eliminando circuitos do ExpressRoute não provisionados

O Assistente identifica circuitos do ExpressRoute que estão no status de provedor *Não Provisionado* por mais de um mês e recomenda excluir o circuito se você não estiver planejando provisioná-lo com o seu provedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir os custos excluindo ou reconfigurando gateways de rede virtual ociosos

O Assistente identifica os portões de rede virtual ociosos há mais de 90 dias. Como esses gateways são cobradas por hora, você deverá considerar a reconfiguração ou a exclusão deles se não pretender mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Compre instâncias de máquina virtual reservadas para economizar nos custos de pagamento conforme o uso

O Assistente examinará seu uso da máquina virtual nos últimos 30 dias e determinará se você poderá economizar dinheiro com a compra de uma reserva do Azure. O Assistente mostrará as regiões e os tamanhos nos quais você tem maior potencial de economia e mostrará as economias estimadas com a compra de reservas. 

Com as reservas do Azure, você pode comprar previamente os custos de base para as máquinas virtuais. Descontos serão aplicados automaticamente a VMs novas ou existentes com o mesmo tamanho e na mesma região que suas reservas. [Saiba mais sobre as Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Assistente do Azure

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2.  No painel do Assistente, clique na guia **Custo**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de desempenho do Advisor](advisor-cost-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-cost-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-cost-recommendations.md)
