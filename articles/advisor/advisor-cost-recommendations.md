---
title: "Recomendações de custo do Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para otimizar o custo de suas implantações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>Recomendações de custo do Advisor

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **Custo** no painel do Assistente.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando ou desligando instâncias subutilizadas 
Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. O Assistente monitora o uso da máquina virtual durante 14 dias e, depois, identifica as máquinas virtuais com baixa utilização. As máquinas virtuais cuja utilização da CPU é de 5% ou menos e cujo uso de rede é de 7 MB ou menos durante quatro ou mais dias são consideradas máquinas virtuais de baixa utilização.

O Assistente mostra o custo estimado da continuação da execução da máquina virtual, de forma que você possa optar por desligá-la ou redimensioná-la.

Se você deseja ser mais agressivo na identificação de máquinas virtuais subutilizadas, você pode ajustar a regra de média de utilização da CPU para cálculo por assinatura.

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Usar uma solução econômica para gerenciar as metas de desempenho de vários bancos de dados SQL
O Advisor identifica as instâncias do SQL Server que podem se beneficiar da criação de pools de banco de dados elástico. Pools de banco de dados elástico fornecem uma solução simples e econômica para gerenciar as metas de desempenho de vários bancos de dados que têm padrões de uso variáveis. Para obter mais informações sobre os pools elásticos do Azure, consulte [O que é um pool elástico do Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos eliminando circuitos do ExpressRoute não provisionados
O Assistente identifica circuitos do ExpressRoute que estão no status de provedor *Não Provisionado* por mais de um mês e recomenda excluir o circuito se você não estiver planejando provisioná-lo com o seu provedor de conectividade.

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
