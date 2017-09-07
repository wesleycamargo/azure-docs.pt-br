---
title: "Métricas do Azure Monitor – métricas com suporte por tipo de recurso | Microsoft Docs"
description: "Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/31/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 83a32dba7b74a303b7fd2167b5fc41212087f8ca
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Azure Monitor
O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo a criação de gráficos para os mesmos no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Abaixo está uma lista completa de todas as métricas atualmente disponíveis do pipeline de métrica do Azure Monitor.

> [!NOTE]
> Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas. Essa lista inclui apenas as métricas de visualização pública disponíveis usando a visualização pública do pipeline de métrica consolidado do Azure Monitor.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|
|TotalConnectionRequests|Solicitações de conexão totais|Contagem|Média|Solicitações de conexão totais. Estas são chegadas.|
|SuccessfullConnectionsPerSec|Conexões bem-sucedidas por segundo|CountPerSecond|Média|Taxa de conclusões de conexão bem-sucedidas.|
|TotalConnectionFailures|Falhas de conexão totais|Contagem|Média|Total de falhas em tentativas de conexão.|
|CurrentUserSessions|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|
|CommandPoolJobQueueLength|Comprimento da fila de trabalho do pool de comando|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|
|ProcessingPoolJobQueueLength|Comprimento da fila de trabalho do pool de processamento|Contagem|Média|Número de trabalhos não de E/S na fila do pool de threads de processamento.|
|CurrentConnections|Conexão: conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|
|CleanerCurrentPrice|Memória: preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|
|CleanerMemoryShrinkable|Memória: memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|
|CleanerMemoryNonshrinkable|Memória: memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|
|MemoryUsage|Memória: uso de memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Processo\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise de memória xVelocity (VertiPaq), além do limite de memória do mecanismo xVelocity.|
|MemoryLimitHard|Memória: limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|
|MemoryLimitHigh|Memória: limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|
|MemoryLimitLow|Memória: limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|
|MemoryLimitVertiPaq|Memória: VertiPaq do limite de memória|Bytes|Média|Limite na memória, do arquivo de configuração.|
|Cota|Memória: cota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|
|QuotaBlocked|Memória: cota bloqueada|Contagem|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|
|VertiPaqNonpaged|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|
|RowsReadPerSec|Processamento: linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|
|RowsConvertedPerSec|Processamento: linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|
|RowsWrittenPerSec|Processamento: linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|
|CommandPoolBusyThreads|Threads: threads ocupados do pool comando|Contagem|Média|Número de threads ocupados no pool de threads de comando.|
|CommandPoolIdleThreads|Threads: threads ociosos do pool de comandos|Contagem|Média|Número de threads ociosos no pool de threads de comando.|
|LongParsingBusyThreads|Threads: threads ocupados de análise longa|Contagem|Média|Número de threads ocupados no pool de threads de análise longa.|
|LongParsingIdleThreads|Threads: threads ociosos de análise longa|Contagem|Média|Número de threads ociosos no pool de threads de análise longa.|
|LongParsingJobQueueLength|Threads: tamanho da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|
|ProcessingPoolBusyIOJobThreads|Threads: threads de trabalho de E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: threads de trabalho não E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|
|ProcessingPoolIOJobQueueLength|Threads: tamanho da fila de trabalhos de E/S do pool de processamento|Contagem|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: threads de trabalho de E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: threads de trabalho não E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|
|QueryPoolIdleThreads|Threads: threads ociosos do pool de consultas|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|
|QueryPoolJobQueueLength|Threads: tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|
|ShortParsingBusyThreads|Threads: threads ocupados de análise resumida|Contagem|Média|Número de threads ocupados no pool de threads de análise resumida.|
|ShortParsingIdleThreads|Threads: threads ociosos de análise resumida|Contagem|Média|Número de threads ociosos no pool de threads de análise resumida.|
|ShortParsingJobQueueLength|Threads: tamanho da fila de trabalhos de análise resumida|Contagem|Média|Número de trabalhos na fila do pool de threads de análise resumida.|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos de mecanismo de mashup|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|TotalRequests|Total de Solicitações de Gateway|Contagem|Total|Número de solicitações de gateway|
|SuccessfulRequests|Solicitações de Gateway com Êxito|Contagem|Total|Número de solicitações de gateway com êxito|
|UnauthorizedRequests|Solicitações de Gateway não autorizado|Contagem|Total|Número de solicitações de gateway não autorizado|
|FailedRequests|Solicitações de Gateway com Falha|Contagem|Total|Número de falhas em solicitações de gateway|
|OtherRequests|Outras solicitações de gateway|Contagem|Total|Número de outras solicitações de gateway|
|Duração|Duração total de solicitações de gateway|Milissegundos|Average,Maximum|Duração total de solicitações de gateway em milissegundos|
|Capacidade|Capacidade (versão prévia)|Porcentagem|Average,Maximum|Métrica de utilização para o serviço ApiManagement|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|TotalJob|Total de trabalhos|Contagem|Total|O número total de trabalhos|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|
|TotalNodeCount|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|
|LowPriorityCoreCount|Contagem de núcleos LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|
|CreatingNodeCount|Criação de contagem de nós|Contagem|Total|Número de nós sendo criados|
|StartingNodeCount|Contagem inicial dos nós|Contagem|Total|Número de nós iniciais|
|WaitingForStartTaskNodeCount|Contagem de nós para tarefa de inicialização em espera|Contagem|Total|Número de nós aguardando a conclusão da Tarefa de Inicialização|
|StartTaskFailedNodeCount|Falha na contagem de nós para a tarefa de inicialização|Contagem|Total|Número de nós com falha na tarefa de inicialização|
|IdleNodeCount|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|
|OfflineNodeCount|Contagem de nós off-line|Contagem|Total|Número de nós off-line|
|RebootingNodeCount|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|
|ReimagingNodeCount|Contagem de nós refazendo a imagem|Contagem|Total|Número de nós refazendo a imagem|
|RunningNodeCount|Contagem de nós em execução|Contagem|Total|Número de nós em execução|
|LeavingPoolNodeCount|Contagem de nós saindo do pool|Contagem|Total|Número de nós saindo do pool|
|UnusableNodeCount|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|
|PreemptedNodeCount|Contagem de nós com preempção|Contagem|Total|Número de nós com preempção|
|TaskStartEvent|Eventos da tarefa de inicialização|Contagem|Total|Número total de tarefas que iniciaram|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas concluídas|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado com falha|
|PoolCreateEvent|Eventos de criação de pool|Contagem|Total|Número total de pools criados|
|PoolResizeStartEvent|Eventos de início de redimensionamento de pool|Contagem|Total|Número total de tarefas de redimensionamento de pool que iniciaram|
|PoolResizeCompleteEvent|Eventos de conclusão de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool concluídos|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool iniciados|
|PoolDeleteCompleteEvent|Eventos de conclusão de exclusão do pool|Contagem|Total|Número total de exclusões de pool concluídas|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|connectedclients|Clientes conectados|Contagem|Máximo||
|totalcommandsprocessed|Total de Operações|Contagem|Total||
|cachehits|Acertos do Cache|Contagem|Total||
|cachemisses|Erros de Cache|Contagem|Total||
|getcommands|Gets|Contagem|Total||
|setcommands|Sets|Contagem|Total||
|evictedkeys|Chaves removidas|Contagem|Total||
|totalkeys|Total de chaves|Contagem|Máximo||
|expiredkeys|Chaves expiradas|Contagem|Total||
|usedmemory|Memória Usada|Bytes|Máximo||
|usedmemoryRss|Memória RSS Usada|Bytes|Máximo||
|serverLoad|Carga do Servidor|Porcentagem|Máximo||
|cacheWrite|Gravação no Cache|BytesPerSecond|Máximo||
|cacheRead|Cache Lido|BytesPerSecond|Máximo||
|percentProcessorTime|CPU|Porcentagem|Máximo||
|connectedclients0|Clientes conectados (Fragmento 0)|Contagem|Máximo||
|totalcommandsprocessed0|Total de operações (Fragmento 0)|Contagem|Total||
|cachehits0|Ocorrências no cache (Fragmento 0)|Contagem|Total||
|cachemisses0|Perdas no cache (Fragmento 0)|Contagem|Total||
|getcommands0|Gets (Fragmento 0)|Contagem|Total||
|setcommands0|Sets (Fragmento 0)|Contagem|Total||
|evictedkeys0|Chaves removidas (Fragmento 0)|Contagem|Total||
|totalkeys0|Total de Chaves (Fragmento 0)|Contagem|Máximo||
|expiredkeys0|Chaves expiradas (Fragmento 0)|Contagem|Total||
|usedmemory0|Memória usada (Fragmento 0)|Bytes|Máximo||
|usedmemoryRss0|Memória RSS usada (Fragmento 0)|Bytes|Máximo||
|serverLoad0|Carga do servidor (Fragmento 0)|Porcentagem|Máximo||
|cacheWrite0|Gravação no cache (Fragmento 0)|BytesPerSecond|Máximo||
|cacheRead0|Leitura no cache (Fragmento 0)|BytesPerSecond|Máximo||
|percentProcessorTime0|CPU (Fragmento 0)|Porcentagem|Máximo||
|connectedclients1|Clientes conectados (Fragmento 1)|Contagem|Máximo||
|totalcommandsprocessed1|Total de operações (Fragmento 1)|Contagem|Total||
|cachehits1|Ocorrências no cache (Fragmento 1)|Contagem|Total||
|cachemisses1|Perdas no cache (Fragmento 1)|Contagem|Total||
|getcommands1|Gets (Fragmento 1)|Contagem|Total||
|setcommands1|Sets (Fragmento 1)|Contagem|Total||
|evictedkeys1|Chaves removidas (Fragmento 1)|Contagem|Total||
|totalkeys1|Total de Chaves (Fragmento 1)|Contagem|Máximo||
|expiredkeys1|Chaves expiradas (Fragmento 1)|Contagem|Total||
|usedmemory1|Memória usada (Fragmento 1)|Bytes|Máximo||
|usedmemoryRss1|Memória RSS usada (Fragmento 1)|Bytes|Máximo||
|serverLoad1|Carga do servidor (Fragmento 1)|Porcentagem|Máximo||
|cacheWrite1|Gravação no cache (Fragmento 1)|BytesPerSecond|Máximo||
|cacheRead1|Leitura no cache (Fragmento 1)|BytesPerSecond|Máximo||
|percentProcessorTime1|CPU (Fragmento 1)|Porcentagem|Máximo||
|connectedclients2|Clientes conectados (Fragmento 2)|Contagem|Máximo||
|totalcommandsprocessed2|Total de operações (Fragmento 2)|Contagem|Total||
|cachehits2|Ocorrências no cache (Fragmento 2)|Contagem|Total||
|cachemisses2|Perdas no cache (Fragmento 2)|Contagem|Total||
|getcommands2|Gets (Fragmento 2)|Contagem|Total||
|setcommands2|Sets (Fragmento 2)|Contagem|Total||
|evictedkeys2|Chaves removidas (Fragmento 2)|Contagem|Total||
|totalkeys2|Total de Chaves (Fragmento 2)|Contagem|Máximo||
|expiredkeys2|Chaves expiradas (Fragmento 2)|Contagem|Total||
|usedmemory2|Memória usada (Fragmento 2)|Bytes|Máximo||
|usedmemoryRss2|Memória RSS usada (Fragmento 2)|Bytes|Máximo||
|serverLoad2|Carga do servidor (Fragmento 2)|Porcentagem|Máximo||
|cacheWrite2|Gravação no cache (Fragmento 2)|BytesPerSecond|Máximo||
|cacheRead2|Leitura no cache (Fragmento 2)|BytesPerSecond|Máximo||
|percentProcessorTime2|CPU (Fragmento 2)|Porcentagem|Máximo||
|connectedclients3|Clientes conectados (Fragmento 3)|Contagem|Máximo||
|totalcommandsprocessed3|Total de operações (Fragmento 3)|Contagem|Total||
|cachehits3|Ocorrências no cache (Fragmento 3)|Contagem|Total||
|cachemisses3|Perdas no cache (Fragmento 3)|Contagem|Total||
|getcommands3|Gets (Fragmento 3)|Contagem|Total||
|setcommands3|Sets (Fragmento 3)|Contagem|Total||
|evictedkeys3|Chaves removidas (Fragmento 3)|Contagem|Total||
|totalkeys3|Total de Chaves (Fragmento 3)|Contagem|Máximo||
|expiredkeys3|Chaves expiradas (Fragmento 3)|Contagem|Total||
|usedmemory3|Memória usada (Fragmento 3)|Bytes|Máximo||
|usedmemoryRss3|Memória RSS usada (Fragmento 3)|Bytes|Máximo||
|serverLoad3|Carga do servidor (Fragmento 3)|Porcentagem|Máximo||
|cacheWrite3|Gravação no cache (Fragmento 3)|BytesPerSecond|Máximo||
|cacheRead3|Leitura no cache (Fragmento 3)|BytesPerSecond|Máximo||
|percentProcessorTime3|CPU (Fragmento 3)|Porcentagem|Máximo||
|connectedclients4|Clientes conectados (Fragmento 4)|Contagem|Máximo||
|totalcommandsprocessed4|Total de operações (Fragmento 4)|Contagem|Total||
|cachehits4|Ocorrências no cache (Fragmento 4)|Contagem|Total||
|cachemisses4|Perdas no cache (Fragmento 4)|Contagem|Total||
|getcommands4|Gets (Fragmento 4)|Contagem|Total||
|setcommands4|Sets (Fragmento 4)|Contagem|Total||
|evictedkeys4|Chaves removidas (Fragmento 4)|Contagem|Total||
|totalkeys4|Total de Chaves (Fragmento 4)|Contagem|Máximo||
|expiredkeys4|Chaves expiradas (Fragmento 4)|Contagem|Total||
|usedmemory4|Memória usada (Fragmento 4)|Bytes|Máximo||
|usedmemoryRss4|Memória RSS usada (Fragmento 4)|Bytes|Máximo||
|serverLoad4|Carga do servidor (Fragmento 4)|Porcentagem|Máximo||
|cacheWrite4|Gravação no cache (Fragmento 4)|BytesPerSecond|Máximo||
|cacheRead4|Leitura no cache (Fragmento 4)|BytesPerSecond|Máximo||
|percentProcessorTime4|CPU (Fragmento 4)|Porcentagem|Máximo||
|connectedclients5|Clientes conectados (Fragmento 5)|Contagem|Máximo||
|totalcommandsprocessed5|Total de operações (Fragmento 5)|Contagem|Total||
|cachehits5|Ocorrências no cache (Fragmento 5)|Contagem|Total||
|cachemisses5|Perdas no cache (Fragmento 5)|Contagem|Total||
|getcommands5|Gets (Fragmento 5)|Contagem|Total||
|setcommands5|Sets (Fragmento 5)|Contagem|Total||
|evictedkeys5|Chaves removidas (Fragmento 5)|Contagem|Total||
|totalkeys5|Total de Chaves (Fragmento 5)|Contagem|Máximo||
|expiredkeys5|Chaves expiradas (Fragmento 5)|Contagem|Total||
|usedmemory5|Memória usada (Fragmento 5)|Bytes|Máximo||
|usedmemoryRss5|Memória RSS usada (Fragmento 5)|Bytes|Máximo||
|serverLoad5|Carga do servidor (Fragmento 5)|Porcentagem|Máximo||
|cacheWrite5|Gravação no cache (Fragmento 5)|BytesPerSecond|Máximo||
|cacheRead5|Leitura no cache (Fragmento 5)|BytesPerSecond|Máximo||
|percentProcessorTime5|CPU (Fragmento 5)|Porcentagem|Máximo||
|connectedclients6|Clientes conectados (Fragmento 6)|Contagem|Máximo||
|totalcommandsprocessed6|Total de operações (Fragmento 6)|Contagem|Total||
|cachehits6|Ocorrências no cache (Fragmento 6)|Contagem|Total||
|cachemisses6|Perdas no cache (Fragmento 6)|Contagem|Total||
|getcommands6|Gets (Fragmento 6)|Contagem|Total||
|setcommands6|Sets (Fragmento 6)|Contagem|Total||
|evictedkeys6|Chaves removidas (Fragmento 6)|Contagem|Total||
|totalkeys6|Total de Chaves (Fragmento 6)|Contagem|Máximo||
|expiredkeys6|Chaves expiradas (Fragmento 6)|Contagem|Total||
|usedmemory6|Memória usada (Fragmento 6)|Bytes|Máximo||
|usedmemoryRss6|Memória RSS usada (Fragmento 6)|Bytes|Máximo||
|serverLoad6|Carga do servidor (Fragmento 6)|Porcentagem|Máximo||
|cacheWrite6|Gravação no cache (Fragmento 6)|BytesPerSecond|Máximo||
|cacheRead6|Leitura no cache (Fragmento 6)|BytesPerSecond|Máximo||
|percentProcessorTime6|CPU (Fragmento 6)|Porcentagem|Máximo||
|connectedclients7|Clientes conectados (Fragmento 7)|Contagem|Máximo||
|totalcommandsprocessed7|Total de operações (Fragmento 7)|Contagem|Total||
|cachehits7|Ocorrências no cache (Fragmento 7)|Contagem|Total||
|cachemisses7|Perdas no cache (Fragmento 7)|Contagem|Total||
|getcommands7|Gets (Fragmento 7)|Contagem|Total||
|setcommands7|Sets (Fragmento 7)|Contagem|Total||
|evictedkeys7|Chaves removidas (Fragmento 7)|Contagem|Total||
|totalkeys7|Total de Chaves (Fragmento 7)|Contagem|Máximo||
|expiredkeys7|Chaves expiradas (Fragmento 7)|Contagem|Total||
|usedmemory7|Memória usada (Fragmento 7)|Bytes|Máximo||
|usedmemoryRss7|Memória RSS usada (Fragmento 7)|Bytes|Máximo||
|serverLoad7|Carga do servidor (Fragmento 7)|Porcentagem|Máximo||
|cacheWrite7|Gravação no cache (Fragmento 7)|BytesPerSecond|Máximo||
|cacheRead7|Leitura no cache (Fragmento 7)|BytesPerSecond|Máximo||
|percentProcessorTime7|CPU (Fragmento 7)|Porcentagem|Máximo||
|connectedclients8|Clientes conectados (Fragmento 8)|Contagem|Máximo||
|totalcommandsprocessed8|Total de operações (Fragmento 8)|Contagem|Total||
|cachehits8|Ocorrências no cache (Fragmento 8)|Contagem|Total||
|cachemisses8|Perdas no cache (Fragmento 8)|Contagem|Total||
|getcommands8|Gets (Fragmento 8)|Contagem|Total||
|setcommands8|Sets (Fragmento 8)|Contagem|Total||
|evictedkeys8|Chaves removidas (Fragmento 8)|Contagem|Total||
|totalkeys8|Total de Chaves (Fragmento 8)|Contagem|Máximo||
|expiredkeys8|Chaves expiradas (Fragmento 8)|Contagem|Total||
|usedmemory8|Memória usada (Fragmento 8)|Bytes|Máximo||
|usedmemoryRss8|Memória RSS usada (Fragmento 8)|Bytes|Máximo||
|serverLoad8|Carga do servidor (Fragmento 8)|Porcentagem|Máximo||
|cacheWrite8|Gravação no cache (Fragmento 8)|BytesPerSecond|Máximo||
|cacheRead8|Leitura no cache (Fragmento 8)|BytesPerSecond|Máximo||
|percentProcessorTime8|CPU (Fragmento 8)|Porcentagem|Máximo||
|connectedclients9|Clientes conectados (Fragmento 9)|Contagem|Máximo||
|totalcommandsprocessed9|Total de operações (Fragmento 9)|Contagem|Total||
|cachehits9|Ocorrências no cache (Fragmento 9)|Contagem|Total||
|cachemisses9|Perdas no cache (Fragmento 9)|Contagem|Total||
|getcommands9|Gets (Fragmento 9)|Contagem|Total||
|setcommands9|Sets (Fragmento 9)|Contagem|Total||
|evictedkeys9|Chaves removidas (Fragmento 9)|Contagem|Total||
|totalkeys9|Total de Chaves (Fragmento 9)|Contagem|Máximo||
|expiredkeys9|Chaves expiradas (Fragmento 9)|Contagem|Total||
|usedmemory9|Memória usada (Fragmento 9)|Bytes|Máximo||
|usedmemoryRss9|Memória RSS usada (Fragmento 9)|Bytes|Máximo||
|serverLoad9|Carga do servidor (Fragmento 9)|Porcentagem|Máximo||
|cacheWrite9|Gravação no cache (Fragmento 9)|BytesPerSecond|Máximo||
|cacheRead9|Leitura no cache (Fragmento 9)|BytesPerSecond|Máximo||
|percentProcessorTime9|CPU (Fragmento 9)|Porcentagem|Máximo||

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|
|Bytes de gravação de disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|
|Bytes de gravação de disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|TotalCalls|Total de Chamadas|Contagem|Total|Número total de chamadas.|
|SuccessfulCalls|Chamadas com Êxito|Contagem|Total|Número de chamadas com êxito.|
|TotalErrors|Total de Erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|
|BlockedCalls|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|
|ServerErrors|Erros do Servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|
|ClientErrors|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|
|DataIn|Entrada de Dados|Bytes|Total|Tamanho dos dados de entrada em bytes.|
|DataOut|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|
|Latência|Latência|MilliSeconds|Média|Latência em milissegundos.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Total de bytes lidos do disco durante o período de monitoramento|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Total de bytes gravados em disco durante o período de monitoramento|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Total de bytes lidos do disco durante o período de monitoramento|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Total de bytes gravados em disco durante o período de monitoramento|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Total de bytes lidos do disco durante o período de monitoramento|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Total de bytes gravados em disco durante o período de monitoramento|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|DCIApiCalls|Chamadas à API do Customer Insights|Contagem|Total||
|DCIMappingImportOperationSuccessfulLines|Linhas bem-sucedidas da operação de importação de mapeamento|Contagem|Total||
|DCIMappingImportOperationFailedLines|Linhas com falha da operação de importação de mapeamento|Contagem|Total||
|DCIMappingImportOperationTotalLines|Total de linhas da operação de importação de mapeamento|Contagem|Total||
|DCIMappingImportOperationRuntimeInSeconds|Tempo de execução da operação de importação de mapeamento em segundos|Segundos|Total||
|DCIOutboundProfileExportSucceeded|Exportação de perfil de saída com êxito|Contagem|Total||
|DCIOutboundProfileExportFailed|Exportação de perfil de saída com falha|Contagem|Total||
|DCIOutboundProfileExportDuration|Duração da exportação de perfil de saída|Segundos|Total||
|DCIOutboundKpiExportSucceeded|Exportação de KPI de saída com êxito|Contagem|Total||
|DCIOutboundKpiExportFailed|Exportação de KPI de saída com falha|Contagem|Total||
|DCIOutboundKpiExportDuration|Duração da exportação de KPI de saída|Segundos|Total||
|DCIOutboundKpiExportStarted|Início da exportação de KPI de saída|Segundos|Total||
|DCIOutboundKpiRecordCount|Contagem de registros de KPI de saída|Segundos|Total||
|DCIOutboundProfileExportCount|Contagem de exportações de perfil de saída|Segundos|Total||
|DCIOutboundInitialProfileExportFailed|Exportação de perfil inicial de saída com falha|Segundos|Total||
|DCIOutboundInitialProfileExportSucceeded|Exportação de perfil inicial de saída com êxito|Segundos|Total||
|DCIOutboundInitialKpiExportFailed|Exportação de KPI inicial de saída com falha|Segundos|Total||
|DCIOutboundInitialKpiExportSucceeded|Exportação de KPI inicial de saída com êxito|Segundos|Total||
|DCIOutboundInitialProfileExportDurationInSeconds|Duração da exportação de perfil inicial de saída em segundos|Segundos|Total||
|AdlaJobForStandardKpiFailed|Trabalho do ADLA para KPI padrão com falha em segundos|Segundos|Total||
|AdlaJobForStandardKpiTimeOut|Trabalho do ADLA para KPI padrão com TimeOut em segundos|Segundos|Total||
|AdlaJobForStandardKpiCompleted|Trabalho do ADLA para KPI padrão concluído em segundos|Segundos|Total||
|ImportASAValuesFailed|Contagem de valores do ASA de importação com falha|Contagem|Total||
|ImportASAValuesSucceeded|Contagem de valores do ASA de importação com êxito|Contagem|Total||

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos com êxito.|
|JobEndedFailure|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|
|JobEndedCancelled|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|
|JobAUEndedSuccess|Tempo de AU com êxito|Segundos|Total|Tempo total de AU dos trabalhos com êxito.|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU dos trabalhos com falha.|
|JobAUEndedCancelled|Tempo de AU cancelada|Segundos|Total|Tempo total de AU dos trabalhos cancelados.|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|TotalStorage|Armazenamento total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|
|DataWritten|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos na conta.|
|WriteRequests|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|
|ReadRequests|Solicitações de leitura|Contagem|Total|Contagem solicitações de leitura de dados para a conta.|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|
|compute_limit|Limite de Unidade de Computação|Contagem|Média|Limite de Unidade de Computação|
|compute_consumption_percent|Percentual de Unidade de Computação|Porcentagem|Média|Percentual de Unidade de Computação|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|
|active_connections|Total de conexões ativas|Contagem|Média|Total de conexões ativas|
|connections_failed|Total de conexões com falha|Contagem|Média|Total de conexões com falha|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|
|compute_limit|Limite de Unidade de Computação|Contagem|Média|Limite de Unidade de Computação|
|compute_consumption_percent|Percentual de Unidade de Computação|Porcentagem|Média|Percentual de Unidade de Computação|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|
|active_connections|Total de conexões ativas|Contagem|Média|Total de conexões ativas|
|connections_failed|Total de conexões com falha|Contagem|Média|Total de conexões com falha|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|
|c2d.commands.egress.complete.success|Comandos concluídos|Contagem|Total|Número de comandos da nuvem para o dispositivo concluídos com sucesso pelo dispositivo|
|c2d.commands.egress.abandon.success|Comandos abandonados|Contagem|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|
|c2d.commands.egress.reject.success|Comandos rejeitados|Contagem|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|
|devices.totalDevices|Total de dispositivos|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Contagem|Total|Número de dispositivos registrados ao seu hub IoT|
|d2c.telemetry.egress.success|Mensagens de telemetria entregues|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade (total)|
|d2c.telemetry.egress.dropped|Mensagens descartadas|Contagem|Total|Número de mensagens removidas porque o ponto de extremidade de entrega estava inativo|
|d2c.telemetry.egress.orphaned|Mensagens órfãs|Contagem|Total|A contagem de mensagens que não correspondem a nenhuma rota, incluindo a rota de fallback|
|d2c.telemetry.egress.invalid|Mensagens inválidas|Contagem|Total|A contagem de mensagens não entregues devido à incompatibilidade com o ponto de extremidade|
|d2c.telemetry.egress.fallback|Mensagens que correspondem à condição de fallback|Contagem|Total|Número de mensagens gravadas para o ponto de extremidade de fallback|
|d2c.endpoints.egress.eventHubs|Mensagens entregues aos pontos de extremidade do Hub de Evento|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade do Hub de Evento|
|d2c.endpoints.latency.eventHubs|Latência de mensagem para pontos de extremidade do Hub de Eventos|Milissegundos|Média|A latência média entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos, em milissegundos|
|d2c.endpoints.egress.serviceBusQueues|Mensagens entregues aos pontos de extremidade da Fila do Barramento de Serviço|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade da Fila do Barramento de Serviço|
|d2c.endpoints.latency.serviceBusQueues|Latência de mensagem para pontos de extremidade da Fila do Barramento de Serviço|Milissegundos|Média|A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de Fila do Barramento de Serviço, em milissegundos|
|d2c.endpoints.egress.serviceBusTopics|Mensagens entregues aos pontos de extremidade do Tópico do Barramento de Serviço|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade do Tópico do Barramento de Serviço|
|d2c.endpoints.latency.serviceBusTopics|Latência de mensagem para pontos de extremidade de Tópico do Barramento de Serviço|Milissegundos|Média|A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de Tópico do Barramento de Serviço, em milissegundos|
|d2c.endpoints.egress.builtIn.events|Mensagens entregues ao ponto de extremidade interno (mensagens/eventos)|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito no ponto de extremidade interno (mensagens/eventos)|
|d2c.Endpoints.Latency.builtIn.Events|Latência de mensagem para o ponto de extremidade interno (mensagens/eventos)|Milissegundos|Média|A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem no ponto de extremidade interno (mensagens/eventos), em milissegundos |
|d2c.endpoints.egress.storage|Mensagens entregues aos pontos de extremidade de armazenamento|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade de armazenamento|
|d2c.endpoints.latency.storage|Latência de mensagem para pontos de extremidade de armazenamento|Milissegundos|Média|A latência média entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de armazenamento, em milissegundos|
|d2c.endpoints.egress.storage.bytes|Dados gravados no armazenamento|Bytes|Total|Quantidade de dados, em bytes, gravados nos pontos de extremidade de armazenamento|
|d2c.endpoints.egress.storage.blobs|Blobs gravados no armazenamento|Contagem|Total|Número de blobs gravados nos pontos de extremidade de armazenamento|
|d2c.twin.read.success|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|d2c.twin.read.failure|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|
|d2c.twin.read.size|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|d2c.twin.update.success|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|d2c.twin.update.failure|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|
|d2c.twin.update.size|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|c2d.methods.success|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|
|c2d.methods.failure|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|
|c2d.methods.requestSize|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|
|c2d.methods.responseSize|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|
|c2d.twin.read.success|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|
|c2d.twin.read.failure|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|
|c2d.twin.read.size|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|
|c2d.twin.update.success|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|
|c2d.twin.update.failure|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|
|c2d.twin.update.size|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|
|twinQueries.success|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|
|twinQueries.failure|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|
|twinQueries.resultSize|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|
|jobs.createTwinUpdateJob.success|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|
|jobs.createTwinUpdateJob.failure|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|
|jobs.createDirectMethodJob.failure|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|
|jobs.listJobs.success|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|
|jobs.listJobs.failure|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|
|jobs.queryJobs.success|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|
|jobs.queryJobs.failure|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|
|jobs.failed|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens usadas hoje|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|INREQS|Solicitações de envio recebidas|Contagem|Total|Total de solicitações de envio recebidas para um hub de notificação|
|SUCCREQ|Solicitações bem sucedidas|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|
|FAILREQ|Solicitações com falha|Contagem|Total|Total de solicitações com falha para um namespace|
|SVRBSY|Erros de servidor ocupado|Contagem|Total|Total de erros de servidor ocupado para um namespace|
|INTERR|Erros internos do servidor|Contagem|Total|Total de erros internos de servidor para um namespace|
|MISCERR|Outros erros|Contagem|Total|Total de solicitações com falha para um namespace|
|INMSGS|Mensagens de entrada|Contagem|Total|Total de mensagens de entrada para um namespace|
|OUTMSGS|Mensagens de saída|Contagem|Total|Total de mensagens de saída para um namespace|
|EHINMBS|Bytes de entrada|Bytes|Total|Taxa de transferência de mensagem de entrada do Hub de evento para um namespace|
|EHOUTMBS|Bytes de saída|Bytes|Total|Total de mensagens de saída para um namespace|
|EHABL|Arquivar mensagens da lista de pendências|Contagem|Total|Mensagens no arquivo morto do Hub de evento na lista de pendências para um namespace|
|EHAMSGS|Arquivar mensagens|Contagem|Total|Mensagens no arquivo morto do Hub de eventos em um namespace|
|EHAMBS|Arquivar taxa de transferência de mensagem|Bytes|Total|Taxa de transferência de mensagens arquivadas do Hub de eventos em um namespace|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|
|RunsSucceeded|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|
|RunLatency|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|
|RunSuccessLatency|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|
|RunThrottledEvents|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|
|RunFailurePercentage|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|
|ActionsSucceeded|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|
|ActionsFailed|Ações com falha|Contagem|Total|Número de ações de fluxo de trabalho com falha.|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|
|ActionLatency|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|
|ActionSuccessLatency|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|
|ActionThrottledEvents|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|
|TriggersSucceeded|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|
|TriggerLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|
|TriggerFireLatency|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|
|TriggerSuccessLatency|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|
|TriggerThrottledEvents|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|
|BillableActionExecutions|Execuções de ação faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|
|BillableTriggerExecutions|Execuções de gatilho faturáveis|Contagem|Total|Número de execuções de gatilho do fluxo de trabalho sendo cobradas.|
|TotalBillableExecutions|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo faturadas.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Taxa de transferência|Taxa de transferência|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo Gateway de Aplicativo|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|BytesIn|BytesIn|Contagem|Total|Bytes entrando no Azure|
|BytesOut|BytesOut|Contagem|Total|Bytes saindo do Azure|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|registration.all|Operação de registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (consultas de atualizações de criações e exclusões). |
|registration.create|Operação de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|
|registration.update|Operação de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|
|registration.get|Operações de leitura do registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|
|registration.delete|Operação de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|
|entrada|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API enviadas com sucesso. |
|incoming.scheduled|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|
|incoming.scheduled.cancel|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|
|scheduled.pending|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|
|installation.all|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|
|installation.get|Obter operações de instalação|Contagem|Total|Obter operações de instalação|
|installation.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|
|installation.patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|
|installation.delete|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|
|outgoing.allpns.success|Notificações de sucesso|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|
|outgoing.allpns.invalidpayload|Erros de conteúdo|Contagem|Total|A contagem de pushes que falharam porque o PNS retornou um erro de conteúdo inválido.|
|outgoing.allpns.pnserror|Erros de sistema de notificação externa|Contagem|Total|A contagem de pushes que falharam porque houve um problema na comunicação com o PNS (exclui problemas de autenticação).|
|outgoing.allpns.channelerror|Erros de canal|Contagem|Total|A contagem de pushes que falharam porque o canal era inválido, não associado com o aplicativo correto, restrito ou expirado.|
|outgoing.allpns.badorexpiredchannel|Erros de canal incorreto ou expirado|Contagem|Total|A contagem de pushes que falharam porque o canal/token/registrationId do registro estava expirado ou inválido.|
|outgoing.wns.success|Notificações do WNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|
|outgoing.wns.invalidcredentials|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|
|outgoing.wns.badchannel|Erro de canal inválido do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do WNS: 404 não encontrado).|
|outgoing.wns.expiredchannel|Erro de canal expirado do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 perdido).|
|outgoing.wns.throttled|Notificações restritas do WNS|Contagem|Total|A contagem de pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|
|outgoing.wns.tokenproviderunreachable|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|
|outgoing.wns.invalidtoken|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|
|outgoing.wns.wrongtoken|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 proibido). Isso pode acontecer se o ChannelURI do registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no hub de notificação.|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todos os conteúdos inválidos.|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido do WNS|Contagem|Total|O conteúdo de notificação é muito grande (status do WNS: 413).|
|outgoing.wns.channelthrottled|Canal do WNS restrito|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-NotificationStatus:channelThrottled).|
|outgoing.wns.channeldisconnected|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: disconnected).|
|outgoing.wns.dropped|Notificações do WNS descartadas|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|
|outgoing.wns.pnserror|Erros do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|
|outgoing.wns.authenticationerror|Erros de autenticação do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, credenciais inválidas ou token incorreto.|
|outgoing.apns.success|Notificações do APNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|
|outgoing.apns.invalidcredentials|Erros de autorização do APNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|
|outgoing.apns.badchannel|Erro de canal inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o token é inválido (código de status do APNS: 8).|
|outgoing.apns.expiredchannel|Erro de canal expirado do APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (código de status do APNS: 7).|
|outgoing.apns.pnserror|Erros do APNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o APNS.|
|outgoing.gcm.success|Notificações do GCM bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|
|outgoing.gcm.invalidcredentials|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|
|outgoing.gcm.badchannel|Erro de canal inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado ou inválido (resultado GCM: registo inválido).|
|outgoing.gcm.expiredchannel|Erro de canal expirado do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado (resultado do GCM: NotRegistered).|
|outgoing.gcm.throttled|Notificações restritas do GCM|Contagem|Total|A contagem de pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501 a 599 ou resultado: indisponível).|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo não foi formatado corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|
|outgoing.gcm.wrongchannel|Erro de canal incorreto do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|
|outgoing.gcm.pnserror|Erros do GCM|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o GCM.|
|outgoing.gcm.authenticationerror|Erros de autenticação do GCM|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas, as credenciais estão bloqueadas ou o SenderId não está configurado corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|
|outgoing.mpns.success|Notificações de sucesso do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|
|outgoing.mpns.invalidcredentials|Credenciais inválidas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|
|outgoing.mpns.badchannel|Erro de canal inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do MPNS: 404 não encontrado).|
|outgoing.mpns.throttled|Notificações restritas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|
|outgoing.mpns.invalidnotificationformat|Erro de tamanho de notificação inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo da notificação era muito grande.|
|outgoing.mpns.channeldisconnected|Canal do MPNS desconectado|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro foi desconectado (status do MPNS: 412 não encontrado).|
|outgoing.mpns.dropped|Notificações descartadas do MPNS|Contagem|Total|A contagem de pushes que foram descartados pelo MPNS (cabeçalho de resposta do MPNS: X-NotificationStatus: QueueFull ou Suppressed).|
|outgoing.mpns.pnserror|Erros do MPNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o MPNS.|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|
|notificationhub.pushes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|
|incoming.all.requests|Todas as solicitações recebidas|Contagem|Total|Total de solicitações recebidas para um hub de notificação|
|incoming.all.failedrequests|Todas as solicitações com falha recebidas|Contagem|Total|Total de solicitações com falha recebidas para um hub de notificação|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa do serviço de pesquisa|
|SearchQueriesPerSecond|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|
|ThrottledSearchQueriesPercentage|Porcentagem das consultas de pesquisa limitadas|Porcentagem|Média|Porcentagem de consultas de pesquisa que eram limitadas para o serviço de pesquisa|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CPUXNS|Uso da CPU por namespace|Porcentagem|Máximo|Métrica de uso de CPU do namespace premium do barramento de serviço|
|WSXNS|Uso do tamanho da memória por namespace|Porcentagem|Máximo|Métrica de uso de memória do namespace premium do barramento de serviço|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|
|storage|Tamanho total do banco de dados|Bytes|Máximo|Tamanho total do banco de dados|
|connection_successful|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|
|connection_failed|Conexões com falha|Contagem|Total|Conexões com falha|
|blocked_by_firewall|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|
|deadlock|Deadlocks|Contagem|Total|Deadlocks|
|storage_percent|Percentual de tamanho do banco de dados|Porcentagem|Máximo|Percentual de tamanho do banco de dados|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|
|dtu_limit|Limite de DTU|Contagem|Média|Limite de DTU|
|dtu_used|DTU usado|Contagem|Média|DTU usado|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU|
|dwu_consumption_percent|Porcentagem de DWU|Porcentagem|Máximo|Porcentagem de DWU|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|
|database_cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|
|database_physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|
|database_log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|
|database_workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|
|database_sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|
|eDTU_limit|Limite de eDTU|Contagem|Média|Limite de eDTU|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|
|database_storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|
|database_storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|ResourceUtilization|% de utilização do SU|Porcentagem|Máximo|% de utilização do SU|
|InputEvents|Eventos de entrada|Contagem|Total|Eventos de entrada|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|
|LateInputEvents|Eventos de entrada atrasados|Contagem|Total|Eventos de entrada atrasados|
|OutputEvents|Eventos de saída|Contagem|Total|Eventos de saída|
|ConversionErrors|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|
|Erros|Erros de tempo de execução|Contagem|Total|Erros de tempo de execução|
|DroppedOrAdjustedEvents|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|
|AMLCalloutRequests|Solicitações de função|Contagem|Total|Solicitações de função|
|AMLCalloutFailedRequests|Solicitações de função com falha|Contagem|Total|Solicitações de função com falha|
|AMLCalloutInputEvents|Eventos de função|Contagem|Total|Eventos de função|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|
|DiskQueueLength|Tamanho da fila do disco|Contagem|Total|Tamanho da fila do disco|
|HttpQueueLength|Tamanho da Fila de Http|Contagem|Total|Tamanho da Fila de Http|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções)

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|
|Solicitações|Solicitações|Contagem|Total|Solicitações|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|
|Http101|Http 101|Contagem|Total|Http 101|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|
|Http401|Http 401|Contagem|Total|Http 401|
|Http403|Http 403|Contagem|Total|Http 403|
|Http404|Http 404|Contagem|Total|Http 404|
|Http406|Http 406|Contagem|Total|Http 406|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|
|FunctionExecutionUnits|Unidades de Execução de Função|Contagem|Média|Unidades de Execução de Função|
|FunctionExecutionCount|Contagem de Execução de Função|Contagem|Média|Contagem de Execução de Função|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|
|Solicitações|Solicitações|Contagem|Total|Solicitações|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|
|Http101|Http 101|Contagem|Total|Http 101|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|
|Http401|Http 401|Contagem|Total|Http 401|
|Http403|Http 403|Contagem|Total|Http 403|
|Http404|Http 404|Contagem|Total|Http 404|
|Http406|Http 406|Contagem|Total|Http 406|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|
|FunctionExecutionUnits|Unidades de Execução de Função|Contagem|Média|Unidades de Execução de Função|
|FunctionExecutionCount|Contagem de Execução de Função|Contagem|Média|Contagem de Execução de Função|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Solicitações|Solicitações|Contagem|Total|Solicitações|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|
|Http101|Http 101|Contagem|Total|Http 101|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|
|Http401|Http 401|Contagem|Total|Http 401|
|Http403|Http 403|Contagem|Total|Http 403|
|Http404|Http 404|Contagem|Total|Http 404|
|Http406|Http 406|Contagem|Total|Http 406|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|
|DiskQueueLength|Tamanho da fila do disco|Contagem|Total|Tamanho da fila do disco|
|HttpQueueLength|Tamanho da Fila de Http|Contagem|Total|Tamanho da Fila de Http|
|ActiveRequests|Solicitações ativas|Contagem|Total|Solicitações ativas|
|TotalFrontEnds|Total de front-ends|Contagem|Média|Total de front-ends|
|SmallAppServicePlanInstances|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|
|MediumAppServicePlanInstances|Funções de trabalho médias do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho médias do Plano do Serviço de Aplicativo|
|LargeAppServicePlanInstances|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho grandes do Plano do Serviço de Aplicativo|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|WorkersTotal|Total de funções de trabalho|Contagem|Média|Total de funções de trabalho|
|WorkersAvailable|Funções de trabalho disponíveis|Contagem|Média|Funções de trabalho disponíveis|
|WorkersUsed|Funções de trabalho usadas|Contagem|Média|Funções de trabalho usadas|

## <a name="next-steps"></a>Próximas etapas
* [Leia sobre as métricas no Azure Monitor](monitoring-overview-metrics.md)
* [Criar alertas para métricas](insights-receive-alert-notifications.md)
* [Exportar as métricas de armazenamento, Hub de eventos ou Log Analytics](monitoring-overview-of-diagnostic-logs.md)

