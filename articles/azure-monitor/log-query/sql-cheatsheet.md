---
title: Folha de referências de SQL para a consulta de log do Azure Monitor | Microsoft Docs
description: Ajuda para usuários que estão familiarizados com o SQL para escrever consultas de log no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 76c9e143a4127807bfffa879103fca42d2d27e71
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268315"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Folha de referências de SQL para a consulta de log do Azure Monitor 

A tabela abaixo ajuda os usuários que estão familiarizados com o SQL a aprender a linguagem de consulta do Kusto para gravar consultas de log no Azure Monitor. Dê uma olhada no comando T-SQL para resolver um cenário comum e o equivalente usando a consulta de log do Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL para Azure Monitor

DESCRIÇÃO                             |Consulta SQL                                                                                          |Consulta de log do Azure Monitor
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Selecionar todos os dados de uma tabela            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Selecionar colunas específicas em uma tabela    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Selecionar 100 registros em uma tabela         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Avaliação de nulo                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Comparação de cadeias de caracteres: igualdade             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Comparação de cadeias de caracteres: substring            |`SELECT * FROM dependencies WHERE like "%bcd%"`                                                    |<code>dependencies <br>&#124; where name contains "bcd"</code>
Comparação de cadeias de caracteres: curinga             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Comparação de datas: último 1 dia             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Comparação de datas: intervalo de datas             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Comparação de boolianos                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Classificar                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Agrupamento, Agregação                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliases de coluna, Estender                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
n registros principais por medida                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
União                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
União: com condições                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions value < 5`              |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Ingressar                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Próximas etapas

- Faça uma lição sobre como [escrever consultas de log no Azure Monitor](get-started-queries.md).