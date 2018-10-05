---
title: Monitoramento e ajuste de desempenho – Banco de Dados SQL do Azure | Microsoft Docs
description: Dicas de ajuste de desempenho no Banco de Dados SQL por meio de avaliação e melhoria.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165034"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoramento e ajuste de desempenho

O Banco de Dados SQL do Azure é um serviço de dados flexível e gerenciado automaticamente no qual você pode facilmente monitorar o uso, adicionar ou remover recursos (CPU, memória, E/S), encontrar recomendações que podem melhorar o desempenho do banco de dados ou permitir que o banco de dados se adapte à carga de trabalho e otimize o desempenho automaticamente.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Visão geral do monitoramento do desempenho do banco de dados no Banco de Dados SQL do Azure
O monitoramento do desempenho de um banco de dados SQL no Azure começa com o monitoramento da utilização de recursos em relação ao nível de desempenho de banco de dados escolhido. O monitoramento ajuda a determinar se o banco de dados tem excesso de capacidade ou se está enfrentando problemas por ter atingido o máximo de recursos. Com essas informações, é possível decidir se é momento de ajustar o tamanho da computação e as camadas de serviços do banco de dados no [modelo de compras baseado em DTU](sql-database-service-tiers-dtu.md) ou no [modelo de compras baseado em vCore](sql-database-service-tiers-vcore.md). Você pode monitorar o banco de dados no [portal do Azure](https://portal.azure.com) usando as seguintes ferramentas gráficas ou as [DMVs (exibições de gerenciamento dinâmico)](sql-database-monitoring-with-dmvs.md) do SQL.

O Banco de Dados SQL do Azure permite identificar oportunidades para melhorar e otimizar o desempenho de consultas sem alterar recursos examinando as [recomendações de ajuste de desempenho](sql-database-advisor.md). Índices ausentes e consultas precárias são motivos comuns para um desempenho ruim do banco de dados. Aplique essas recomendações de ajuste para melhorar o desempenho de sua carga de trabalho.
Você também pode permitir que o banco de dados SQL do Azure [otimize o desempenho das consultas automaticamente](sql-database-automatic-tuning.md), aplicando todas as recomendações identificadas e verificando se elas melhoram o desempenho do banco de dados. Você tem as seguintes opções para monitorar e solucionar problemas de desempenho do banco de dados:

- No [portal do Azure](https://portal.azure.com), clique em **Bancos de dados SQL**, selecione o banco de dados e, depois, use o gráfico de Monitoramento para procurar recursos que estão atingindo o limite. O consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
- Use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para identificar as consultas que gastam a maioria dos recursos.
- Use o [Assistente do Banco de Dados SQL](sql-database-advisor-portal.md) para exibir recomendações para criar e remover índices, parametrizar consultas e corrigir problemas de esquema.
- Use [Insights inteligentes do SQL do Azure](sql-database-intelligent-insights.md) para monitoramento automático do desempenho do banco de dados. Quando um problema de desempenho é detectado, um log de diagnóstico é gerado com detalhes e RCA (Análise da Causa Raiz) do problema. Recomendação de melhoria de desempenho é fornecida quando possível.
- [Habilite o ajuste automático](sql-database-automatic-tuning-enable.md) e permita que o banco de dados SQL do Azure corrija os problemas de desempenho identificados automaticamente.
- Você também pode usar as [DMVs (exibições de gerenciamento dinâmico)](sql-database-monitoring-with-dmvs.md), [eventos estendidos (`XEvents`)(sql-database/sql-database-xevent-db-diff-from-svr.md)e o [Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para obter os parâmetros de desempenho em tempo real. Consulte as [diretrizes de desempenho](sql-database-performance-guidance.md) para descobrir técnicas que você pode usar para melhorar o desempenho do Banco de Dados SQL do Azure, caso identifique algum problema ao usar esses relatórios ou essas exibições.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorar bancos de dados usando o Portal do Azure
No [Portal do Azure](https://portal.azure.com/), é possível monitorar a utilização de um banco de dados individual selecionando-o e clicando no gráfico **Monitoramento** . Isso abre uma janela **Métrica** que pode ser alterada clicando no botão **Editar gráfico**. Adicione as seguintes métricas:

* Percentual de CPU
* Porcentagem de DTU
* Porcentagem de E/S de dados
* Percentual de tamanho do banco de dados

Depois de adicionar essas métricas, você pode continuar a exibi-las no gráfico **Monitoramento** com mais informações na janela **Métrica**. Todas as quatro métricas mostram o percentual médio de utilização relativo à **DTU** do seu banco de dados. Consulte os artigos [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) para obter mais informações sobre as camadas de serviço.  

![Monitoramento da camada de serviço do desempenho do banco de dados.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Você também pode configurar alertas nas métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métrica**. Siga o Assistente para configurar o alerta. Você tem a opção de alerta se a métrica exceder um limite determinado ou se ficar abaixo de um limite determinado.

Por exemplo, se você espera que a carga de trabalho em seu banco de dados cresça, você poderá configurar um alerta por email sempre que seu banco de dados atinge 80% em qualquer uma das métricas de desempenho. Você pode usar isso como um aviso antecipado para decidir quando precisará alternar para o próximo tamanho mais alto de computação.

As métricas de desempenho podem ajudá-lo a determinar se você pode fazer downgrade para um tamanho de computação inferior. Suponha que você está usando um banco de dados Standard S2 e todas as métricas de desempenho mostram que o banco de dados em média não usa mais de 10% a qualquer momento. É provável que o banco de dados funcione bem em Standard S1. No entanto, tome cuidado com cargas de trabalho que apresentam picos ou oscilam antes de tomar a decisão de migrar para um tamanho de computação inferior.

## <a name="improving-database-performance-with-more-resources"></a>Melhorando o desempenho do banco de dados com mais recursos

Por fim, se não houver nenhum item acionável que possa melhorar o desempenho do banco de dados, você poderá alterar a quantidade de recursos disponíveis no Banco de Dados SQL do Azure. É possível atribuir mais recursos alterando a [camada de serviço DTU](sql-database-service-tiers-dtu.md) de um banco de dados individual ou aumentar as eDTUs de um pool elástico a qualquer momento. Como alternativa, se estiver usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), você poderá alterar a camada de serviço ou aumentar os recursos alocados para o banco de dados. 
1. Para bancos de dados individuais, é possível [alterar as camadas de serviço](sql-database-service-tiers-dtu.md) ou os [recursos de computação](sql-database-service-tiers-vcore.md) sob demanda para melhorar o desempenho do banco de dados.
2. Para vários bancos de dados, considere o uso de [pools elásticos](sql-database-elastic-pool-guidance.md) para dimensionar os recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Ajustar e refatorar o código do aplicativo ou do banco de dados

Você pode alterar o código do aplicativo para usar o banco de dados de forma mais otimizada, alterar índices, forçar planos ou usar dicas para adaptar manualmente o banco de dados à sua carga de trabalho. Encontre algumas diretrizes e dicas para o ajuste manual e a reescrita do código no artigo [tópico de diretrizes de desempenho](sql-database-performance-guidance.md).


## <a name="next-steps"></a>Próximas etapas

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure e permitir que o recurso de ajuste automático gerencie a carga de trabalho por completo, consulte [Habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, examine as [Recomendações de ajuste no portal do Azure](sql-database-advisor-portal.md) e aplique manualmente aquelas que melhoram o desempenho de suas consultas.
- Altere os recursos que estão disponíveis no banco de dados alterando as [camadas de serviço do Banco de Dados SQL do Azure](sql-database-performance-guidance.md)
