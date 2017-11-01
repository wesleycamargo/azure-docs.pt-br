---
title: "Monitorar o uso do banco de dados com o Intelligent Insights – Banco de dados SQL do Azure| Microsoft Docs"
description: "O Intelligent Insights do Banco de Dados SQL do Azure usa uma inteligência interna para monitorar continuamente o uso de banco de dados por meio de inteligência artificial e detecta os eventos de interrupção que causam baixo desempenho."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 7e56baab6ae161736b05ea53b59c9e802a94633e
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="intelligent-insights"></a>Insights inteligentes

O recurso Intelligent Insights do Banco de Dados SQL do Azure permite que você saiba o que está acontecendo com o desempenho do banco de dados.

O Intelligent Insights usa uma inteligência interna para monitorar continuamente o uso de banco de dados por meio de inteligência artificial e detecta os eventos de interrupção que causam baixo desempenho. Quando detectada, é executada uma análise detalhada que gera um log de diagnóstico com uma avaliação inteligente do problema. Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho. 

## <a name="what-can-intelligent-insights-do-for-you"></a>O que o recurso Insights inteligentes pode fazer por você?

O Intelligent Insights é uma capacidade exclusiva de inteligência interna do Azure e fornece o seguinte valor:

- Monitoramento proativo
- Insights de desempenho personalizado
- Detecção prévia de degradação de desempenho do banco de dados
- Análise da causa raiz dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como funciona o recurso Insights inteligentes?

O Intelligent Insights analisa o desempenho do Banco de Dados SQL comparando a carga de trabalho do banco de dados da última hora com a carga de trabalho de linha de base dos últimos sete dias. A carga de trabalho do banco de dados é composta por consultas determinadas a serem mais significativas ao desempenho do banco de dados, como as consultas mais repetidas e maiores. Uma vez que banco de dados é exclusivo com base em estrutura, dados, uso e aplicativo, cada linha de base da carga de trabalho gerada é específica e exclusiva a uma instância individual. O recurso Insights inteligentes, independentemente da linha de base da carga de trabalho, também monitora limites operacionais absolutos e detecta problemas com tempos de espera excessivos, exceções críticas e problemas com parametrizações de consulta que podem afetar o desempenho.

Após a detecção de um problema de degradação do desempenho de várias métricas observadas usando inteligência artificial, a análise é executada. O log de diagnóstico é gerado com um insight inteligente sobre o que está acontecendo com seu banco de dados. O Insights inteligentes facilita o acompanhamento do problema de desempenho do banco de dados, da primeira ocorrência à solução. Cada problema detectado é rastreado em todo o ciclo de vida, da detecção inicial do problema e verificação da melhora de desempenho até sua conclusão. São fornecidas atualizações no log de diagnóstico a cada 15 minutos. 

![Fluxo de trabalho de análise de desempenho do banco de dados](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

A métrica usada para medir e detectar problemas de desempenho do banco de dados é baseada na duração da consulta, em solicitações que atingem o tempo limite, tempos de espera excessivo e solicitações com erros. Para obter mais informações sobre métricas, consulte a seção [Métricas de detecção](sql-database-intelligent-insights.md#detection-metrics) deste documento.

## <a name="degradations-detected"></a>Degradações detectadas

As degradações de desempenho do Banco de dados SQL identificadas são registradas no log de diagnóstico com entradas inteligentes que consistem nas seguintes propriedades:

| Propriedade             | Detalhes              |
| :------------------- | ------------------- |
| Informações de banco de dados | Metadados sobre um banco de dados no qual foi detectado um insight, como o URI do recurso. |
| Intervalo de tempo observado | Hora de início e término do período do insight detectado. |
| Métricas afetadas | Métricas que fizeram uma visão mais detalhada ser gerada: <ul><li>Aumento da duração da consulta [segundos].</li><li>Espera excessiva [segundos].</li><li>Solicitações com tempo limite [percentual].</li><li>Solicitações com erro [percentual].</li></ul>|
| Valor do impacto | Valor de uma métrica medida. |
| Consultas afetadas e códigos de erro | Hash de consulta ou código de erro. Podem ser usados para correlacionar facilmente a consultas afetadas. São fornecidas métricas que consistem em aumento de duração da consulta, tempo de espera, contagens de tempo limite ou códigos de erro. |
| Detecções | Detecção identificada no banco de dados durante o tempo de um evento. Há 15 padrões de detecção. Para obter mais informações, consulte [Solucionar problemas de desempenho do banco de dados com o Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Análise da causa raiz | Análise da causa raiz do problema identificado em um formato legível por humanos. Alguns insights podem conter recomendação de melhoria de desempenho sempre que possível. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-complete"></a>Ciclo de vida de estado dos problemas: “Ativo”, “Verificando” e “Concluído”

Problemas de desempenho registrados no log de diagnóstico são marcados com um dos três estados do ciclo de vida do problema: “Ativo”, “Verificando” e “Concluído”. Após um problema de desempenho ser detectado e pelo tempo que ele for considerado presente pela inteligência interna do Banco de Dados SQL, o problema será marcado como "Ativo". Quando o problema é considerado reduzido, ele é verificado e o status do problema é alterado para "Verificando". Depois que inteligência interna do Banco de Dados SQL considera o problema resolvido, o status do problema é sinalizado como "Completo".

## <a name="use-intelligent-insights"></a>Usar Intelligent Insights

Você pode enviar o log de diagnóstico do Intelligent Insights para o Azure Log Analytics, Hubs de Eventos do Azure e Armazenamento do Azure. Para obter mais informações, consulte [Métricas de Banco de Dados SQL do Azure e log de diagnóstico](sql-database-metrics-diag-logging.md). Depois de enviar o log a um desses destinos, o log pode ser usado para alertas personalizados e desenvolvimento de monitoramento usando ferramentas Microsoft ou de terceiros. 

Para obter mais informações sobre a solução de problemas de desempenho do Banco de Dados SQL do Azure usando Intelligent Insights, consulte [Solucionar problemas de desempenho do Banco de dados SQL do Azure com Insights inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="built-in-intelligent-insights-analytics-with-log-analytics"></a>Análise interna do Intelligent Insights com o Log Analytics 

A solução Log Analytics fornece recursos de relatórios e alertas, além dos dados de log de diagnóstico de Insights inteligentes. O exemplo a seguir mostra um relatório do Intelligent Insights na Análise de SQL do Azure:

![Relatório de Insights inteligentes](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Depois que o log de diagnóstico do Intelligent Insights é configurado para transmitir dados para Análise de SQL, você pode [monitorar o banco de dados SQL usando a Análise de SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas de log do Insights inteligentes

Para obter mais informações sobre alertas personalizados e monitoramento do desenvolvimento usando ferramentas Microsoft ou de terceiros, consulte [Usar o log de diagnóstico de desempenho de banco de dados do Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Configurar o Intelligent Insights com Hubs de Eventos

- Para configurar o Intelligent Insights para transmitir eventos de log a Hubs de Eventos, consulte [Transmitir logs de diagnóstico do Azure para Hubs de Eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- Use o Hub de Eventos para monitoramento e alertas personalizados; consulte [O que fazer com métricas e logs de diagnóstico no Hub de Eventos](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Configurar o Intelligent Insights com o armazenamento

- Configure o Intelligent Insights para ser armazenado com o Armazenamento, consulte [Transmitir para o Armazenamento do Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="detection-metrics"></a>Métricas de detecção

Métricas usadas para modelos de detecção que geram insights inteligentes são baseadas em monitoramento:

- Duração da consulta
- Solicitações de tempo limite
- Tempo de espera excessivo
- Solicitações com erro

Solicitações de duração e tempo limite de consulta são usadas como modelos principais na detecção de problemas de desempenho de carga de trabalho de banco de dados. Elas são usadas porque medem diretamente o que está acontecendo com a carga de trabalho. Para detectar todos os casos possíveis de degradação do desempenho de carga de trabalho, tempo de espera excessivo e solicitações com erros são usados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema automaticamente considera alterações na carga de trabalho e no número de solicitações de consulta feitas ao banco de dados para determinar dinamicamente os limites normais e fora do comum de desempenho do banco de dados.

Todas as métricas são consideradas em conjunto em vários relacionamentos por meio de um modelo de dados derivado cientificamente que categoriza cada problema de desempenho detectado. As informações fornecidas por meio de um insight inteligente incluem:

* Detalhes do problema de desempenho detectado. 
* Uma análise da causa raiz do problema detectado. 
* Recomendações sobre como melhorar o desempenho do banco de dados SQL monitorado, quando possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação da duração da consulta analisa consultas individuais e detecta o aumento no tempo necessário para compilar e executar uma consulta em comparação à linha de base de desempenho.

Se a inteligência interna do Banco de Dados SQL do Azure detectar um aumento significativo no tempo de execução ou na compilação da consulta que afeta o desempenho da carga de trabalho, essas consultas serão sinalizadas como problemas de degradação de desempenho de duração da consulta. 

O log de diagnóstico do Intelligent Insights gera o hash de consulta da consulta com degradação de desempenho. O hash de consulta indica se a degradação do desempenho foi relacionada a um aumento no tempo de execução ou compilação de consulta, que aumentou o tempo de duração da consulta.

## <a name="timeout-requests"></a>Solicitações de tempo limite

O modelo de degradação das solicitações de tempo limite analisa consultas individuais e detecta qualquer aumento nos tempos limite no nível de execução da consulta e nos limites de solicitação geral no nível do banco de dados em comparação ao período de linha de base de desempenho.

Algumas das consultas podem atingir o tempo limite até mesmo antes de atingirem o estágio de execução. Por meio de trabalhadores anulados versus solicitações feitas, a inteligência interna do Banco de Dados SQL mede e analisa todas as consultas que chegaram ao banco de dados, tenham chegado ao estágio de execução ou não. 

Depois que o número de tempos limite das consultas executadas ou o número de trabalhadores com solicitação anuladas cruza o limite gerenciado do sistema, um log de diagnóstico é preenchido com insights inteligentes.

Os insights gerados contêm o número de solicitações que atingiram o tempo limite e o número de consultas que atingiram o tempo limite. A indicação da degradação de desempenho está relacionada ao aumento de tempo limite no estágio de execução ou ao nível de banco de dados geral fornecido. Quando o aumento nos tempos limite é considerado significativo para o desempenho do banco de dados, essas consultas são sinalizadas como problemas de degradação de desempenho de tempo limite. 

## <a name="excessive-wait-times"></a>Tempos excessivos de espera

O modelo de tempo de espera excessivo monitora as consultas de banco de dados individuais. Ele detecta estatísticas de espera de consulta incomumente altas que cruzaram os limites absolutos gerenciado pelo sistema. As seguintes métricas de tempo de espera excessivo são observadas utilizando o novo recurso do SQL Server, Estatísticas de Espera de Repositório de Consultas (sys.query_store_wait_stats):

- Atingindo os limites do recurso
- Atingindo os limites de recurso do pool elástico
- Número excessivo de threads de trabalho ou sessão
- Bloqueio excessivo do banco de dados
- Demanda de memória
- Outras estatísticas de espera

Atingir os limites de recurso ou os limites de recursos do pool elástico denota que o consumo de recursos disponíveis em uma assinatura ou no pool elástico cruzou limites absolutos. Essas estatísticas indicam degradação do desempenho de carga de trabalho. Um número excessivo de threads de trabalho ou sessão denota uma condição em que o número de threads de trabalho ou sessões iniciadas cruzou os limites absolutos. Essas estatísticas indicam degradação do desempenho de carga de trabalho.

Bloqueio de banco de dados excessivo indica uma condição em que a contagem de bloqueios em um banco de dados cruzou os limites absolutos. Esta estatística indica uma degradação de desempenho da carga de trabalho. A pressão de memória é uma condição em que o número de threads solicitando concessões de memória cruzou um limite absoluto. Esta estatística indica uma degradação de desempenho da carga de trabalho.

A detecção de outras estatísticas de espera indica uma condição em que métricas diversas medidas por meio das Estatísticas de Espera de Repositório de Consultas cruzaram um limite absoluto. Essas estatísticas indicam degradação do desempenho de carga de trabalho.

Depois que forem detectados tempos de espera excessivo, dependendo dos dados disponíveis, o log de diagnóstico de Insights inteligentes produz hashes das consultas afetadas e que afetam o sistema degradadas no desempenho, dos detalhes das métricas que fazem as consultas esperarem em execução e do tempo de espera medido.

## <a name="errored-requests"></a>Solicitações com erros

O modelo de degradação de solicitações com erros monitora consultas individuais e detecta um aumento no número de consultas que apresentaram erros em comparação com o período da linha de base. Esse modelo também monitora exceções críticas que cruzaram limites absolutos gerenciados pela inteligência interna do Banco de Dados SQL. O sistema automaticamente considera o número de solicitações de consulta feitas ao banco de dados e considera quaisquer alterações de carga de trabalho no período monitorado.

Quando o aumento medido em solicitações com erros em relação o número total de solicitações feitas é considerado significativo para o desempenho da carga de trabalho, as consultas afetadas são sinalizadas como problemas de degradação do desempenho de solicitações com erros.

O log do Intelligent Insights gera a contagem de solicitações com erros. Indica se a degradação do desempenho estava relacionada a um aumento nas solicitações com erros ou a cruzar um limite de exceção crítico monitorado e tempo medido da degradação do desempenho. 

Se qualquer uma das exceções críticas monitoradas cruzar os limites absolutos gerenciados pelo sistema, um insight inteligente será gerado com detalhes da exceção crítica.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Solucionar problemas de desempenho do Banco de Dados SQL com Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Use o [log de diagnóstico de desempenho do Banco de dados SQL com Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
* Saiba como [monitorar o Banco de Dados SQL por meio de Análise de SQL](../log-analytics/log-analytics-azure-sql.md).
* Saiba como [coletar e consumir dados de log dos recursos do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


