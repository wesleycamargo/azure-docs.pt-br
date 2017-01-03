---
title: Monitorar o desempenho de banco de dados no Banco de Dados SQL do Azure| Microsoft Docs
description: "Saiba mais sobre as opções de monitoramento de seu banco de dados com as ferramentas do Azure e exibições de gerenciamento dinâmico."
keywords: monitoramento de banco de dados, desempenho do banco de dados em nuvem
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/27/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3ba16154857f8e7b59a1013b736d6131a4161185
ms.openlocfilehash: f4150149cea4fa31e7b991e15ffcbefa8a202ada


---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorar o desempenho do banco de dados no Banco de Dados SQL do Azure
O monitoramento do desempenho de um banco de dados SQL no Azure começa com o monitoramento da utilização de recursos em relação ao nível de desempenho de banco de dados escolhido. O monitoramento ajuda você a determinar se o seu banco de dados tem excesso de capacidade ou se está enfrentando problemas por ter atingido o máximo de recursos. Com essas informações, você pode decidir se é hora de ajustar o nível do desempenho e a [camada de serviço](sql-database-service-tiers.md) do banco de dados. Você pode monitorar o banco de dados usando ferramentas gráficas no [Portal do Azure](https://portal.azure.com) ou as [exibições de gerenciamento dinâmico](https://msdn.microsoft.com/library/ms188754.aspx) do SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorar bancos de dados usando o Portal do Azure
No [Portal do Azure](https://portal.azure.com/), é possível monitorar a utilização de um banco de dados individual selecionando-o e clicando no gráfico **Monitoramento** . Isso abre uma janela **Métrica** que pode ser alterada clicando no botão **Editar gráfico**. Adicione as seguintes métricas:

* Percentual de CPU
* Porcentagem de DTU
* Porcentagem de E/S de dados
* Percentual de tamanho do banco de dados

Depois de adicionar essas métricas, você pode continuar a exibi-las no gráfico **Monitoramento** com mais detalhes na janela **Métrica**. Todas as quatro métricas mostram o percentual médio de utilização relativo à **DTU** do seu banco de dados. Confira o artigo [camadas de serviço](sql-database-service-tiers.md) para obter detalhes sobre DTUs.

![Monitoramento da camada de serviço do desempenho do banco de dados.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Você também pode configurar alertas nas métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métrica**. Siga o Assistente para configurar o alerta. Você tem a opção de alerta se a métrica exceder um limite determinado ou se ficar abaixo de um limite determinado.

Por exemplo, se você espera que a carga de trabalho em seu banco de dados cresça, você poderá configurar um alerta por email sempre que seu banco de dados atinge 80% em qualquer uma das métricas de desempenho. Você pode usar isso como um aviso antecipado para decidir quando precisará alternar para o próximo nível mais alto de desempenho.

As métricas de desempenho podem ajudá-lo a determinar se você pode fazer downgrade para um nível de desempenho inferior. Suponha que você está usando um banco de dados Standard S2 e todas as métricas de desempenho mostram que o banco de dados em média não usa mais de 10% a qualquer momento. É provável que o banco de dados funcione bem em Standard S1. No entanto, tome cuidado com cargas de trabalho que apresentam picos ou oscilam antes de tomar a decisão de migrar para um nível de desempenho inferior.

## <a name="monitor-databases-using-dmvs"></a>Monitorar bancos de dados usando DMVs
As mesmas métricas que são expostas no portal também estão disponíveis por meio de exibições do sistema: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) no banco de dados **mestre** lógico do seu servidor e [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) no banco de dados do usuário. Use **sys.resource_stats** se precisar monitorar dados menos granulares em um período de tempo mais longo. Use **sys.dm_db_resource_stats** se precisar monitorar dados mais granulares em um período mais curto. Para obter mais informações, veja [Orientação sobre o desempenho do Banco de Dados SQL do Azure](sql-database-performance-guidance.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** retorna um conjunto de resultados vazio quando usado em bancos de dados Web e Business Edition, que estão desativados.
>
>

Para pools elásticos, você pode monitorar bancos de dados autônomos no pool com as técnicas descritas nesta seção. Mas você também pode monitorar o pool como um todo. Para saber mais, veja [Monitorar e gerenciar um pool elásticos](sql-database-elastic-pool-manage-portal.md).



<!--HONumber=Dec16_HO3-->


