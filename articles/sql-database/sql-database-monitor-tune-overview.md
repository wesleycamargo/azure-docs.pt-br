---
title: Monitoramento e ajuste de desempenho – Banco de Dados SQL do Azure | Microsoft Docs
description: Dicas de ajuste de desempenho no Banco de Dados SQL do Azure por meio de avaliação e melhoria.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2a7a6ed5bd28bcc83500da6e82b6c4ff48b2989c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097769"
---
# <a name="monitoring-and-performance-tuning"></a>Monitoramento e ajuste de desempenho

O Banco de Dados SQL do Azure é um serviço de dados flexível e gerenciado automaticamente no qual você pode facilmente monitorar o uso, adicionar ou remover recursos (CPU, memória, E/S), encontrar recomendações que podem melhorar o desempenho do banco de dados ou permitir que o banco de dados se adapte à carga de trabalho e otimize o desempenho automaticamente.

## <a name="monitoring-database-performance"></a>Monitorando o desempenho do banco de dados

O monitoramento do desempenho de um banco de dados SQL no Azure começa com o monitoramento da utilização de recursos em relação ao nível de desempenho de banco de dados escolhido. O Banco de Dados SQL do Azure permite identificar oportunidades para melhorar e otimizar o desempenho de consultas sem alterar recursos examinando as [recomendações de ajuste de desempenho](sql-database-advisor.md). Índices ausentes e consultas precárias são motivos comuns para um desempenho ruim do banco de dados. Aplique essas recomendações de ajuste para melhorar o desempenho de sua carga de trabalho. Você também pode permitir que o banco de dados SQL do Azure [otimize o desempenho das consultas automaticamente](sql-database-automatic-tuning.md), aplicando todas as recomendações identificadas e verificando se elas melhoram o desempenho do banco de dados.

Você tem as seguintes opções para monitorar e solucionar problemas de desempenho do banco de dados:

- No [portal do Azure](https://portal.azure.com), clique em **Bancos de dados SQL**, selecione o banco de dados e, depois, use o gráfico de Monitoramento para procurar recursos que estão atingindo o limite. O consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
- Use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para identificar as consultas que gastam a maioria dos recursos.
- Use o [Assistente do Banco de Dados SQL](sql-database-advisor-portal.md) para exibir recomendações para criar e remover índices, parametrizar consultas e corrigir problemas de esquema.
- Use [Insights inteligentes do SQL do Azure](sql-database-intelligent-insights.md) para monitoramento automático do desempenho do banco de dados. Quando um problema de desempenho é detectado, um log de diagnóstico é gerado com detalhes e RCA (Análise da Causa Raiz) do problema. Recomendação de melhoria de desempenho é fornecida quando possível.
- [Habilite o ajuste automático](sql-database-automatic-tuning-enable.md) e permita que o banco de dados SQL do Azure corrija os problemas de desempenho identificados automaticamente.
- Use [exibições de gerenciamento dinâmico (DMVs)](sql-database-monitoring-with-dmvs.md), [eventos estendidos](sql-database-xevent-db-diff-from-svr.md) e a [Loja de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para solucionar problemas mais detalhados de desempenho.

> [!TIP]
> Consulte [orientação de desempenho](sql-database-performance-guidance.md) para encontrar técnicas que você pode usar para melhorar o desempenho do Banco de Dados SQL do Azure depois de identificar o problema de desempenho usando um ou mais dos métodos acima.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorar bancos de dados usando o Portal do Azure

No [portal do Azure](https://portal.azure.com/), você pode monitorar a utilização de um banco de dados individual s selecionando seu banco de dados e clicando na **monitoramento** gráfico. Isso abre uma janela **Métrica** que pode ser alterada clicando no botão **Editar gráfico**. Adicione as seguintes métricas:

- Percentual de CPU
- Porcentagem de DTU
- Porcentagem de E/S de dados
- Percentual de tamanho do banco de dados

Depois de adicionar essas métricas, você pode continuar a visualizá-las no gráfico **Monitoramento** com mais informações na janela **Métrica**. Todas as quatro métricas mostram o percentual médio de utilização relativo à **DTU** do seu banco de dados. Consulte os artigos [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) para obter mais informações sobre as camadas de serviço.  

![Monitoramento da camada de serviço do desempenho do banco de dados.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Você também pode configurar alertas nas métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métrica**. Siga o Assistente para configurar o alerta. Você tem a opção de alerta se a métrica exceder um limite determinado ou se ficar abaixo de um limite determinado.

Por exemplo, se você espera que a carga de trabalho em seu banco de dados cresça, você poderá configurar um alerta por email sempre que seu banco de dados atinge 80% em qualquer uma das métricas de desempenho. Você pode usar isso como um aviso antecipado para decidir quando precisará alternar para o próximo tamanho da computação mais elevado.

As métricas de desempenho podem ajudá-lo a determinar se você pode fazer downgrade para um tamanho da computação inferior. Suponha que você está usando um banco de dados Standard S2 e todas as métricas de desempenho mostram que o banco de dados em média não usa mais de 10% a qualquer momento. É provável que o banco de dados funcione bem em Standard S1. No entanto, tome cuidado com cargas de trabalho que apresentam picos ou oscilam antes de tomar a decisão de migrar para um tamanho da computação inferior.

## <a name="troubleshoot-performance-issues"></a>Solucionar problemas de desempenho

Para diagnosticar e resolver problemas de desempenho, comece Noções básicas sobre o estado de cada consulta ativa e as condições que causam problemas de desempenho relevantes para cada estado da carga de trabalho. Para melhorar o desempenho do Banco de Dados SQL do Azure, entenda que cada solicitação de consulta ativa do seu aplicativo está em execução ou em um estado de espera. Ao solucionar um problema de desempenho no banco de dados SQL do Azure, tenha o gráfico a seguir em mente que você leia este artigo para diagnosticar e resolver problemas de desempenho.

![Estados da carga de trabalho](./media/sql-database-monitor-tune-overview/workload-states.png)

Para uma carga de trabalho com problemas de desempenho, o problema de desempenho pode ser devido à contenção de CPU (uma condição **relacionada à execução)** ou as consultas individuais estão aguardando algo (uma condição**relacionada à espera**).

## <a name="running-related-performance-issues"></a>Problemas de desempenho relacionados à execução

Como diretriz geral, se a utilização da CPU estiver consistentemente ou acima de 80%, você tem um problema de desempenho relacionados à execução. Se você tiver um problema de execução, ele pode ser causado por recursos insuficientes de CPU ou ele pode estar relacionado a uma das seguintes condições:

- Número excessivo de consultas em execução
- Número excessivo de consultas em compilação
- Uma ou mais consultas em execução estão usando um plano de consulta ideal

Se você determinar que você tem um problema de desempenho relacionados à execução, seu objetivo é identificar o problema exato usando um ou mais métodos. Os métodos mais comuns para identificar problemas de execução são:

- Use o [portal do Azure](#monitor-databases-using-the-azure-portal) para monitorar a utilização de percentual de CPU.
- Use as [exibições de gerenciamento dinâmico a seguir](sql-database-monitoring-with-dmvs.md):

  - [sys.DM db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna o consumo de CPU, memória e e/s para um Banco de Dados SQL do Microsoft Azure. Existe uma linha para cada 15 segundos, mesmo se não houver nenhuma atividade no banco de dados. Dados históricos são mantidos por uma hora.
  - [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retorna uso de CPU e dados de armazenamento para um Banco de Dados SQL do Microsoft Azure. Os dados são coletados e agregados em intervalos de cinco minutos.

> [!IMPORTANT]
> Para um conjunto de consultas do T-SQL usando esses DMVs para solucionar problemas de utilização da CPU, consulte [Identificar problemas de desempenho da CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Solucionar problemas de consultas com problemas de plano de execução de consulta sensíveis a parâmetro

O problema de PSP (plano confidencial de parâmetro) se refere a um cenário em que o otimizador de consulta gera um plano de execução de consulta ideal para o valor de parâmetro específico (ou conjunto de valores) e o plano armazenado em cache é não ideal para valores de parâmetro usados em execuções consecutivas. Os planos não ideais então podem resultar em problemas de desempenho de consulta e degradação de taxa de transferência de carga de trabalho geral. Para obter mais informações sobre detecção de parâmetro e o processamento de consultas, consulte o [guia de arquitetura de processamento de consultas](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Há várias soluções alternativas, usadas para atenuar os problemas, cada uma com seus prós e contras:

- Use a dica de consulta [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) em cada execução da consulta. Essa solução negocia o tempo de compilação e aumenta a CPU para melhorar a qualidade do plano. Muitas vezes não é possível usar a opção `RECOMPILE` para cargas de trabalho que exigem uma alta taxa de transferência.
- Use a dica de consulta [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor de parâmetro real por um valor de parâmetro típico que produz um plano bom o suficiente para a maioria das possibilidades de valor de parâmetro.   Essa opção requer uma boa compreensão dos valores de parâmetro ideais e características de plano associadas.
- Use a dica de consulta [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para substituir o valor do parâmetro real em troca de usar a média do vetor de densidade. Outra maneira de fazer isso é capturar os valores de parâmetro de entrada em variáveis locais e, em seguida, usar as variáveis locais dentro de predicados, em vez de usar parâmetros em si. A densidade média deve ser *boa o suficiente* com esse conserto específico.
- Desabilite totalmente a detecção de parâmetro usando a dica de consulta [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query).
- Use a dica de consulta [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) para evitar recompilações enquanto no cache. Esta solução pressupõe que o plano comum *bom o suficiente* já está no cache. Você também pode desabilitar as atualizações automáticas para estatísticas para reduzir a chance de o plano bom ser removido e um novo plano ruim ser compilado.
- Force o plano usando explicitamente a dica de consulta [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (especificando explicitamente, definindo um plano específico usando o Repositório de Consultas ou habilitando [Ajuste Automático](sql-database-automatic-tuning.md).
- Substitua o procedimento único por um conjunto aninhado de procedimentos que podem, cada um, ser usado com base em lógica condicional e nos valores de parâmetro associados.
- Crie alternativas de execução de cadeia de caracteres dinâmica para uma definição de procedimento estático.

Para obter informações adicionais sobre como resolver esses tipos de problemas, confira:

- Isso [eu senti um parâmetro](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) postagem de blog
- Esta postagem no blog [dynamic sql versus plan quality for parameterized queries](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) (sql dinâmico vs. qualidade de plano para consultas parametrizadas)
- Isso [técnicas de otimização de consulta SQL no SQL Server: Detecção de parâmetros](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) postagem de blog

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Solução de problemas de atividade de compilação devido a parametrização inadequada

Quando uma consulta tem literais, o mecanismo de banco de dados escolhe automaticamente parametrizar a instrução ou um usuário pode parametrizá-la explicitamente para reduzir o número de compilações. Um grande número de compilações de uma consulta usando o mesmo padrão, mas valores literais diferentes, pode resultar em alta utilização da CPU. Da mesma forma, se você parametrizar apenas parcialmente uma consulta que continua a ter literais, o mecanismo de banco de dados não a parametrizará ainda mais.  Abaixo está um exemplo de uma consulta parametrizada parcialmente:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

No exemplo anterior, `t1.c1` usa `@p1`, mas `t2.c2` continua a usar GUID como literal. Nesse caso, se você alterar o valor para `c2`, a consulta será tratada como uma consulta diferente e uma nova compilação ocorrerá. Para reduzir as compilações no exemplo anterior, a solução é parametrizar também o GUID.

A consulta a seguir mostra a contagem de consultas por hash de consulta para determinar se uma consulta está parametrizada corretamente ou não:

```sql
SELECT  TOP 10  
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p 
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs 
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Resolver consultas com problema ou fornecer mais recursos

Depois de identificar o problema, você pode ajustar as consultas problemáticas ou atualizar o tamanho da computação ou a camada de serviço para aumentar a capacidade do banco de dados SQL do Azure para absorver os requisitos da CPU. Para obter informações sobre dimensionamento de recursos para bancos de dados únicos, consulte [Recursos de banco de dados único de escala no Banco de Dados SQL do Azure](sql-database-single-database-scale.md) e para dimensionar recursos para conjuntos elásticos, consulte [Recursos do conjunto elástico de escala no Banco de Dados SQL do Azure](sql-database-elastic-pool-scale.md). Para obter informações sobre o dimensionamento de uma instância gerenciada, consulte [Limites de recursos no nível da instância](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Determinar se problemas de execução devem-se ao aumento de volume de carga de trabalho

Um aumento no tráfego de aplicativo e na carga de trabalho pode responder pelo aumento na utilização da CPU, mas você deve ter cuidado para diagnosticar corretamente esse problema. Em um cenário de alto uso de CPU, responda a essas perguntas para determinar se um aumento de CPU de fato se deve a alterações de volume de carga de trabalho:

1. As consultas do aplicativo são a causa do problema de CPU alta?
2. Para as consultas que mais consomem CPU (que podem ser identificadas):

   - Determine se havia vários planos de execução associados à mesma consulta. Se for o caso, determine por quê.
   - Para consultas com o mesmo plano de execução, determine se os tempos de execução eram consistentes e se a contagem de execução aumentou. Em caso afirmativo, há problemas de desempenho provavelmente devido ao aumento da carga de trabalho.

Para resumir, se o plano de execução de consulta não tiver sido executado de modo diferente, mas a utilização da CPU tiver aumentado juntamente com a contagem de execução, provavelmente haverá um problema de desempenho relacionado ao aumento de carga de trabalho.

Nem sempre é fácil concluir que há uma alteração de volume de carga de trabalho que está levando a um problema de CPU.   Fatores a considerar: 

- **O uso do recurso mudou**

  Por exemplo, considere um cenário em que a CPU tenha aumentado para 80% por um longo período.  A utilização da CPU sozinha não significa que o volume de carga de trabalho mudou.  Regressões do plano de execução de consulta e mudanças na distribuição de dados também podem contribuir para mais uso de recursos, mesmo que o aplicativo esteja executando exatamente a mesma carga de trabalho.

- **Apareceu uma nova consulta**

   Um aplicativo pode conduzir um novo conjunto de consultas em momentos diferentes.

- **O número de solicitações aumentou ou diminuiu**

   Esse cenário é a medida mais óbvia da carga de trabalho. O número de consultas nem sempre corresponde a uma maior utilização de recursos. No entanto, essa métrica ainda é um sinal significativo, supondo que outros fatores sejam iguais.

## <a name="waiting-related-performance-issues"></a>Problemas de desempenho relacionados à espera

Quando tiver certeza de que você não está enfrentando um problema de desempenho relacionado à execução de CPU alto, você está enfrentando um problema de desempenho relacionado à espera. Ou seja, os recursos da CPU não estão sendo usados eficientemente porque a CPU está aguardando algum outro recurso. Nesse caso, o próximo passo é identificar quais recursos da sua CPU estão aguardando. Os métodos mais comuns para mostrar as principais categorias de tipo de espera:

- O [Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) fornece estatísticas de esperta por consulta ao longo do tempo. No Repositório de Consultas, aguarde os tipos de espera combinados em categorias de espera. O mapeamento das categorias de espera para tipos de espera está disponível em [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) retorna informações sobre todas as esperas encontradas por conversas executadas durante a operação. Você pode usar essa exibição agregada para diagnosticar problemas de desempenho com o Banco de Dados SQL do Azure e também com consultas e lotes específicos.
- [DM os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retorna informações sobre a fila de espera de tarefas que estão esperando algum recurso.

Em cenários de CPU alta, o Repositório de Consultas e as estatísticas de espera nem sempre refletem a utilização da CPU por estes dois motivos:

- Consultas com alto consumo de CPU podem ainda estar em execução e as consultas não foram concluídas
- Consultas com alto consumo de CPU estavam em execução quando um failover ocorreu

Exibições de gerenciamento dinâmico de controle de estatísticas de espera e Repositório de Consultas mostram apenas resultados para consultas bem-sucedidas e que atingiram o tempo limite, não mostram dados para instruções em execução no momento (até que sejam concluídas). A exibição de gerenciamento dinâmico [.sys_dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) permite que você controle as consultas em execução no momento e o tempo de trabalho associado.

Conforme mostrado no gráfico anterior, as esperas mais comuns são:

- Bloqueios (bloqueio)
- E/S
- `tempdb`-relacionados de contenção
- Esperas de concessão de memória

> [!IMPORTANT]
> Para um conjunto um consultas T-SQL usando esses DMVs para solucionar esses problemas relacionados a espera, consulte:
>
> - [Identificar problemas de desempenho de e/s](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar `tempdb` problemas de desempenho](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identificar as esperas de concessão de memória](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox - esperas e travas](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Melhorando o desempenho do banco de dados com mais recursos

Por fim, se não houver nenhum item acionável que possa melhorar o desempenho do banco de dados, você poderá alterar a quantidade de recursos disponíveis no Banco de Dados SQL do Azure. É possível atribuir mais recursos alterando a [camada de serviço DTU](sql-database-service-tiers-dtu.md) de um banco de dados individual ou aumentar as eDTUs de um pool elástico a qualquer momento. Como alternativa, se estiver usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), você poderá alterar a camada de serviço ou aumentar os recursos alocados para o banco de dados.

1. Para bancos de dados individuais, é possível [alterar as camadas de serviço](sql-database-single-database-scale.md) ou os [recursos de computação](sql-database-single-database-scale.md) sob demanda para melhorar o desempenho do banco de dados.
2. Para vários bancos de dados, considere o uso de [pools elásticos](sql-database-elastic-pool-guidance.md) para dimensionar os recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Ajustar e refatorar o código do aplicativo ou do banco de dados

Você pode alterar o código do aplicativo para usar o banco de dados de forma mais otimizada, alterar índices, forçar planos ou usar dicas para adaptar manualmente o banco de dados à sua carga de trabalho. Encontre algumas diretrizes e dicas para o ajuste manual e a reescrita do código no artigo [tópico de diretrizes de desempenho](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Próximos passos

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure e permitir que o recurso de ajuste automático gerencie a carga de trabalho por completo, consulte [Habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, examine as [Recomendações de ajuste no portal do Azure](sql-database-advisor-portal.md) e aplique manualmente aquelas que melhoram o desempenho de suas consultas.
- Altere os recursos que estão disponíveis no banco de dados alterando as [camadas de serviço do Banco de Dados SQL do Azure](sql-database-performance-guidance.md)
