---
title: Monitoramento de Banco de Dados SQL do Azure usando exibições de gerenciamento dinâmico | Microsoft Docs
description: Saiba como detectar e diagnosticar problemas de desempenho comuns usando exibições de gerenciamento dinâmico para monitorar o Banco de Dados SQL do Microsoft Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 08/08/2018
ms.openlocfilehash: 97907eee9982fdf6a804bc13edbf8c14efa4ce42
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161379"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico
O Banco de Dados SQL do Microsoft Azure permite um subconjunto de modos de exibição de gerenciamento dinâmico para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou demoradas, gargalos de recursos, planos de consulta ruins e assim por diante. Este tópico fornece informações sobre como detectar problemas de desempenho comuns usando exibições de gerenciamento dinâmico.

O Banco de Dados SQL oferece suporte parcial para três categorias de exibições de gerenciamento dinâmico:

* Exibições de gerenciamento dinâmico relacionadas ao banco de dados.
* Exibições de gerenciamento dinâmico relacionadas à execução.
* Exibições de gerenciamento dinâmico relacionadas à transação.

Para obter informações detalhadas sobre exibições de gerenciamento dinâmico, consulte [Funções e exibições (Transact-SQL) de gerenciamento dinâmico](https://msdn.microsoft.com/library/ms188754.aspx) nos Manuais Online do SQL Server.

## <a name="permissions"></a>Permissões
No Banco de Dados SQL, consultar uma visualização de gerenciamento dinâmico requer permissões **VIEW DATABASE STATE** . A permissão **VIEW DATABASE STATE** retorna informações sobre todos os objetos no banco de dados atual.
Para conceder a permissão **VIEW DATABASE STATE** a um usuário específico do banco de dados, execute a seguinte consulta:

```GRANT VIEW DATABASE STATE TO database_user; ```

Em uma instância do SQL Server local, as exibições de gerenciamento dinâmico retornam informações de estado do servidor. Em um Banco de Dados SQL, elas retornam informações relacionadas apenas ao seu banco de dados lógico atual.

## <a name="calculating-database-size"></a>Calculando o tamanho do banco de dados
A seguinte consulta retorna o tamanho do seu banco de dados (em megabytes):

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A consulta a seguir retorna o tamanho do dos objetos individuais (em megabytes) no seu banco de dados:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitoramento de conexões
É possível usar a exibição [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) para recuperar informações sobre as conexões estabelecidas com um servidor do Banco de dados SQL do Azure específico e os detalhes de cada conexão. Além disso, a exibição [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) é útil ao recuperar informações sobre todas as conexões de usuário e tarefas internas ativas.
A consulta a seguir recupera as informações sobre a conexão atual:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Ao executar as exibições **sys.dm_exec_requests** e **sys.dm_exec_sessions**, se você tiver a permissão **EXIBIR ESTADO DO BANCO DE DADOS** no banco de dados, verá todas as sessões em execução no banco de dados; caso contrário, verá apenas a sessão atual.
> 
> 

## <a name="monitor-resource-use"></a>Monitorar o uso de recursos

Você pode monitorar o uso de recursos usando a [Análise de Desempenho de Consultas de Banco de Dados SQL](sql-database-query-performance.md) e [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx).

Você também pode monitorar o uso com estes dois modos de exibição:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Você pode usar a exibição [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) em cada banco de dados SQL. A exibição **sys.dm_db_resource_stats** mostra dados de uso de recursos recentes em relação à camada de serviço. A porcentagem média de CPU, E/S de dados, gravações de log e memória é registrada a cada 15 segundos e armazenada por 1 hora.

Como essa exibição oferece uma visão mais granular do uso de recursos, use **sys.dm_db_resource_stats** primeiro para qualquer análise de estado atual ou para solução de problemas. Por exemplo, esta consulta mostra o uso médio e máximo dos recursos do banco de dados atual durante a última hora:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Para outras consultas, consulte os exemplos em [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats
A exibição [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) no banco de dados **mestre** tem mais informações que podem ajudá-lo a monitorar o desempenho do Banco de Dados SQL em sua camada de serviço e tamanho de computação específicos. Os dados são coletados a cada 5 minutos e são mantidos por aproximadamente 14 dias. Essa exibição é útil para uma análise de histórico de longo prazo de como seu banco de dados SQL usa recursos.

O grafo a seguir mostra o uso de recursos da CPU para um banco de dados Premium com o tamanho de computação P2 para cada hora em uma semana. Esse grafo começa em uma segunda-feira, mostra 5 dias úteis e, depois, um fim de semana, em que ocorrem muito menos atividades no aplicativo.

![Uso de recursos de banco de dados SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Segundo os dados, atualmente esse banco de dados tem um pico de carga de CPU de pouco mais de 50% de uso da CPU em relação ao tamanho de computação P2 (meio-dia de terça-feira). Se a CPU for o fator dominante no perfil de recursos do aplicativo, talvez você decida que P2 é o tamanho de computação certo para garantir que a carga de trabalho sempre seja adequada. Se você espera que um aplicativo cresça com o passar do tempo, é uma boa ideia ter um buffer de recursos adicional para que o aplicativo nunca atinja o limite do nível de desempenho. Aumentando o tamanho de computação, você pode ajudar a evitar erros visíveis para os clientes, que podem ocorrer quando um banco de dados não tem capacidade suficiente para processar solicitações de modo eficaz, especialmente em ambientes sensíveis à latência. Um exemplo é um banco de dados que dá suporte a um aplicativo que pinta páginas da Web com base nos resultados de chamadas de banco de dados.

Outros tipos de aplicativos podem interpretar o mesmo grafo de forma diferente. Por exemplo, se um aplicativo tentar processar dados de folha de pagamento por dia e incluir o mesmo gráfico, esse tipo de modelo de "trabalho em lotes" poderá funcionar bem em um tamanho de computação P1. O tamanho de computação P1 tem 100 DTUs, comparadas a 200 DTUs do tamanho de computação P2. O tamanho de computação P1 fornece que metade do desempenho do P2. Portanto, 50% de uso da CPU em P2 equivale a 100% de uso da CPU em P1. Se o aplicativo não tiver tempo limite, talvez não faça diferença se um trabalho demora 2 horas ou 2,5 horas para ser concluído, desde que seja concluído hoje. Um aplicativo dessa categoria provavelmente pode usar um tamanho de computação P1. Você pode tirar proveito do fato de que há períodos do dia em que o uso de recursos é menor, o que significa que um "pico grande" pode ser extrapolado para um dos ciclos mais tarde. O tamanho de computação P1 pode ser bom para esse tipo de aplicativo (e economiza dinheiro), desde que os trabalhos possam ser concluídos no horário todos os dias.

O Banco de Dados SQL do Azure expõe informações de recurso consumido para cada banco de dados ativo na exibição **sys.resource_stats** do banco de dados **mestre** em cada servidor. Os dados na tabela são agregados em intervalos de cinco minutos. Com as camadas de serviço Básica, Standard e Premium, os dados podem demorar mais de 5 minutos para aparecer na tabela, o que significa que esses dados são mais úteis para análise histórica do que para análise em tempo real. Consulte a exibição **sys.resource_stats** para ver o histórico recente de um banco de dados e validar se a reserva escolhida ofereceu o desempenho desejado quando necessário.

> [!NOTE]
> Você precisa estar conectado ao banco de dados **mestre** de seu servidor lógico do banco de dados SQL para consultar **sys.resource_stats** nos exemplos a seguir.
> 
> 

Este exemplo mostra como os dados nesse modo de exibição são expostos:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Exibição do catálogo sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

O exemplo seguinte mostra diferentes maneiras de usar o modo de exibição de catálogo **sys.resource_stats** para obter informações sobre como o banco de dados SQL usa recursos:

1. Para examinar o uso de recursos da última semana para o banco de dados userdb1, você pode executar esta consulta:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Para avaliar como sua carga de trabalho se ajusta ao tamanho de computação, você precisa fazer uma pesquisa detalhada de cada aspecto das métricas de recursos: CPU, leituras, gravações, número de trabalhadores e número de sessões. Veja uma consulta revisada usando **sys.resource_stats** para relatar os valores médio e máximo dessas métricas de recursos:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Com essas informações sobre os valores médio e máximo de cada métrica de recurso, você pode avaliar como sua carga de trabalho se ajusta ao tamanho de computação escolhido. Normalmente, os valores médios de **sys.resource_stats** oferecem uma boa linha de base a ser usada em comparação ao tamanho de destino. Deve ser seu cartão de medida principal. Por exemplo, você pode estar usando a camada de serviço Standard com o tamanho de computação S2. Os percentuais médios de uso da CPU e leituras e gravações de E/S estão abaixo de 40%, o número médio de trabalhadores está abaixo de 50 e o número médio de sessões é inferior a 200. Talvez a carga de trabalho se enquadre no tamanho de computação S1. É fácil ver se o banco de dados se encaixa nos limites de sessão e de trabalho. Para ver se um banco de dados se enquadra em um tamanho de computação inferior em termos de CPU, leituras e gravações, divida o número de DTUs do tamanho de computação inferior pelo número de DTUs do tamanho de computação atual e multiplique o resultado por 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    O resultado é a diferença de desempenho relativa entre os dois tamanhos de computação em percentual. Se seu uso de recursos não ultrapassar esse valor, sua carga de trabalho poderá se enquadrar no tamanho de computação inferior. No entanto, você precisa examinar todos os intervalos de valores de uso de recursos e determinar, pelo percentual, com que frequência a carga de trabalho do banco de dados se enquadraria no tamanho de computação inferior. A consulta a seguir produz o percentual de ajuste por dimensão de recurso, com base no limite de 40% que calculamos neste exemplo:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Com base na camada de serviço do banco de dados, você pode decidir se sua carga de trabalho se enquadra no tamanho de computação inferior. Se a meta de carga de trabalho do banco de dados é de 99,9% e a consulta anterior retorna valores superiores a 99,9% para as três dimensões de recursos, é provável que sua carga de trabalho se enquadre no tamanho de computação inferior.
   
    A avaliação do percentual de ajuste também traz informações que indicam se você deve ir ao próximo tamanho de computação para chegar à sua meta. Por exemplo, userdb1 mostra o seguinte uso da CPU para a semana passada:
   
   | Percentual médio da CPU | Percentual máximo da CPU |
   | --- | --- |
   | 24,5 |100,00 |
   
    A média da CPU é de aproximadamente um quarto do limite do tamanho de computação, o que se ajustaria bem ao tamanho de computação do banco de dados. No entanto, o valor máximo mostra que o banco de dados atinge o limite do tamanho de computação. Você precisa ir para o próximo tamanho de computação? Avalie quantas vezes sua carga de trabalho atinge 100% e compará-la à meta de carga de trabalho de seu banco de dados.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Se essa consulta retornar um valor inferior a 99,9% para qualquer uma das três dimensões de recursos, considere passar para o tamanho de computação superior ou use técnicas de ajuste de aplicativo para reduzir a carga no Banco de Dados SQL.
4. Este exercício também leva em consideração o aumento projetado da carga de trabalho no futuro.

Para pools elásticos, você pode monitorar bancos de dados autônomos no pool com as técnicas descritas nesta seção. Mas você também pode monitorar o pool como um todo. Para saber mais, veja [Monitorar e gerenciar um pool elásticos](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Máximo de solicitações simultâneas
Para ver o número de solicitações simultâneas, execute esta consulta Transact-SQL em seu banco de dados SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Para analisar a carga de trabalho de um banco de dados local do SQL Server, modifique a consulta a fim de filtrar o banco de dados específico que você deseja analisar. Por exemplo, se você tiver um banco de dados local chamado MyDatabase, esta consulta Transact-SQL retornará a contagem de solicitações simultâneas nesse banco de dados:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Isso é apenas um instantâneo em um único ponto no tempo. Para entender melhor sua carga de trabalho e seus requisitos de solicitações simultâneas, você precisará coletar muitas amostras durante algum tempo.

### <a name="maximum-concurrent-logins"></a>Máximo de logons simultâneos
Você pode analisar seus padrões de usuário e de aplicativo para ter uma ideia da frequência de logons. Você também pode executar cargas reais em um ambiente de teste para ter certeza de que não está atingindo esse ou outros limites mencionados neste artigo. Não há uma única consulta ou DMV (exibição de gerenciamento dinâmico) que mostre a contagem de logons simultâneos ou o histórico.

Se vários clientes usarem a mesma cadeia de conexão, o serviço autenticará cada logon. Se 10 usuários se conectassem simultaneamente a um banco de dados usando o mesmo nome de usuário e senha, existiriam 10 logons simultâneos. Esse limite é aplicado apenas durante o logon e a autenticação. Se os mesmos 10 usuários se conectassem ao banco de dados sequencialmente, o número de logons simultâneos nunca seria maior do que 1.

> [!NOTE]
> Atualmente, esse limite não se aplica a bancos de dados em pools elásticos.
> 
> 

### <a name="maximum-sessions"></a>Máximo de sessões
Para ver o número de sessões ativas atuais, execute esta consulta Transact-SQL em seu banco de dados SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se você estiver analisando uma carga de trabalho local do SQL Server, modifique a consulta para se concentrar em um banco de dados específico. Essa consulta ajuda a determinar possíveis necessidades de sessão do banco de dados, caso você esteja considerando movê-lo para o Banco de Dados SQL do Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Novamente, essas consultas retornam uma contagem pontual. Se coletar várias amostras ao longo do tempo, você terá a melhor compreensão do uso de sua sessão.

Para a análise do Banco de Dados SQL, é possível obter estatísticas históricas sobre sessões consultando a visualização [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) e revisando a coluna **active_session_count**. 

## <a name="monitoring-query-performance"></a>Monitoramento de desempenho da consulta
Consultas de execução lenta ou longa podem consumir recursos significativos do sistema. Esta seção demonstra como usar exibições de gerenciamento dinâmico para detectar alguns problemas comuns de desempenho de consulta. Uma referência mais antiga, mas ainda útil para solução de problemas, é o artigo [Solucionando problemas de desempenho no SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) no Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Localizando as principais consultas N
O exemplo a seguir retorna informações sobre as cinco principais consultas classificadas pelo tempo médio de CPU. Este exemplo agrega as consultas de acordo com sua hash de consulta, para que as consultas logicamente equivalentes sejam agrupadas em função de seu consumo de recursos cumulativos.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitoramento de consultas bloqueadas
Consultas lentas ou demoradas podem contribuir para consumo excessivo de recursos e ser a consequência de consultas bloqueadas. A causa do bloqueio pode ser projeto inadequado de aplicativos, planos de consulta incorretos, a falta de índices úteis e assim por diante. Você pode usar o modo de exibição sys.dm_tran_locks para obter informações sobre a atividade de bloqueio atual em seu Banco de dados SQL do Azure. Para ver um exemplo de código, veja [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nos Manuais Online do SQL Server.

### <a name="monitoring-query-plans"></a>Monitoramento de planos de consulta
Um plano de consulta ineficiente também pode aumentar o consumo de CPU. O exemplo a seguir usa a exibição [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) para determinar qual consulta usa a CPU mais cumulativa.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Consulte também
[Introdução ao Banco de Dados SQL](sql-database-technical-overview.md)

