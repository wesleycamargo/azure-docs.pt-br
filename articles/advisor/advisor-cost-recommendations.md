---
title: "Recomendações de custo do Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para otimizar o custo de suas implantações do Azure."
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 39a882a8338ce820c6b9b98f58d1a9db7448c7be

---

# <a name="advisor-cost-recommendations"></a>Recomendações de custo do Advisor

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **Custo** no painel do Advisor.

![Guia Custo do Advisor](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>Máquinas virtuais de baixa utilização 

Embora certos cenários de aplicativo possam resultar na baixa utilização por padrão, normalmente é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. O Advisor monitora a utilização da máquina virtual durante 14 dias e identifica as máquinas virtuais de baixa utilização. As máquinas virtuais cuja utilização da CPU é de 5% ou menos e uso da rede é sete MB ou menos durante quatro ou mais dias, são consideradas máquinas virtuais de baixa utilização.

O Advisor mostra o custo estimado para continuar executando a máquina virtual. Você pode optar por desligar ou redimensioná-la.  

![Recomendações de custo do Advisor para redimensionamento de máquinas virtuais](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>Recomendações de pool de banco de dados elástico do SQL

O Advisor identifica as instâncias do SQL Server que podem se beneficiar da criação de pools de banco de dados elástico. Pools de banco de dados elástico fornecem uma solução simples e econômica para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso variáveis. Para saber mais sobre pools elásticos do Azure, consulte [O que é um pool elástico do Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![Recomendações de custo do Advisor para pools de banco de dados elástico](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Azure Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Mais serviços**, no painel do menu de serviço, role para baixo até **Monitoramento e Gerenciamento** e clique em **Azure Advisor**. Isso inicia o painel do Advisor. 
3. No painel do Advisor, clique na guia **Custo**, selecione a assinatura para a qual você deseja receber recomendações e clique em **Obter recomendações**

> [!NOTE]
> O Azure Advisor gera recomendações para assinaturas em que você recebeu a função de **Proprietário**, **Colaborador** ou **Leitor**.

## <a name="next-steps"></a>Próximas etapas

Veja estes recursos para saber mais sobre as recomendações do Advisor:
-  [Introdução ao Advisor](advisor-overview.md)
-  [Introdução](advisor-get-started.md)
-  [Recomendações de alta disponibilidade do Advisor](advisor-cost-recommendations.md)
-  [Recomendações de segurança do Advisor](advisor-cost-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO3-->


