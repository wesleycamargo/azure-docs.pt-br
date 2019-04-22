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
ms.openlocfilehash: 188a79df99a174436808acd3d964abf9357cf4c0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699314"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduza os custos de serviço usando o Assistente do Azure

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **Custo** no painel do Assistente.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando ou desligando instâncias subutilizadas 

Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. Advisor monitora a utilização de máquinas virtuais por 7 dias e, em seguida, identifica as máquinas virtuais de baixa utilização. Virtual máquinas são consideradas baixa utilização se a utilização da CPU é de 5% ou menos e sua utilização de rede é menor que 2%, ou se a carga de trabalho atual pode ser acomodada em um menor tamanho de máquina virtual.

O Assistente mostra o custo estimado da continuação da execução da máquina virtual, de forma que você possa optar por desligá-la ou redimensioná-la.

Se você deseja ser mais agressivo na identificação de máquinas virtuais subutilizadas, você pode ajustar a regra de média de utilização da CPU para cálculo por assinatura.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos eliminando circuitos do ExpressRoute não provisionados

O Assistente identifica circuitos do ExpressRoute que estão no status de provedor *Não Provisionado* por mais de um mês e recomenda excluir o circuito se você não estiver planejando provisioná-lo com o seu provedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir os custos excluindo ou reconfigurando gateways de rede virtual ociosos

O Assistente identifica os portões de rede virtual ociosos há mais de 90 dias. Como esses gateways são cobradas por hora, você deverá considerar a reconfiguração ou a exclusão deles se não pretender mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Compre instâncias de máquina virtual reservadas para economizar nos custos de pagamento conforme o uso

O Assistente examinará seu uso da máquina virtual nos últimos 30 dias e determinará se você poderá economizar dinheiro com a compra de uma reserva do Azure. O Assistente mostrará as regiões e os tamanhos nos quais você tem maior potencial de economia e mostrará as economias estimadas com a compra de reservas. Com as reservas do Azure, você pode comprar previamente os custos de base para as máquinas virtuais. Descontos serão aplicados automaticamente a VMs novas ou existentes com o mesmo tamanho e na mesma região que suas reservas. [Saiba mais sobre as Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O Supervisor também notificará de instâncias reservadas que você tem que irá expirar nos próximos 30 dias. Ela recomendará que você compre instâncias reservadas novas para evitar pagar o preço pago conforme o uso.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Excluir os endereços IP públicos não podem ser associados para economizar dinheiro

O Advisor identifica os endereços IP públicos que não estão atualmente associados aos recursos do Azure, como VMs ou balanceadores de carga. Esses endereços de IP público endereços vêm com um custo nominal. Se você não planeja usá-los, excluí-los pode resultar em economias de custo.

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
