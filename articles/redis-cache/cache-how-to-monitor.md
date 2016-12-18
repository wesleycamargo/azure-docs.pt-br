---
title: Como monitorar o Cache Redis do Azure | Microsoft Docs
description: "Saiba como monitorar a integridade e o desempenho de suas instâncias do Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9c9022e7cab3537eac19e9c1120fbc67aa122c98


---
# <a name="how-to-monitor-azure-redis-cache"></a>Como monitorar o Cache Redis do Azure
O Cache Redis do Azure fornece várias opções para monitorar suas instâncias de cache. Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem monitorar a integridade de suas instâncias de Cache Redis do Azure e ajudá-lo a gerenciar seus aplicativos de cache.

Quando o diagnóstico de cache é habilitado, métricas para instâncias do Cache Redis do Azure são coletadas aproximadamente a cada 30 segundos e armazenadas para que possam ser exibidas nos gráficos de métricas e avaliadas por regras de alerta.

As métricas de cache são coletadas usando o comando [INFO](http://redis.io/commands/info) do Redis. Para saber mais sobre os diferentes valores INFO usados para cada métrica de cache, confira [Métricas disponíveis e intervalos de relatórios](#available-metrics-and-reporting-intervals).

Para exibir as métricas de cache, [procure](cache-configure.md#configure-redis-cache-settings) sua instância de cache no [portal do Azure](https://portal.azure.com). As métricas para instâncias do Cache Redis do Azure são acessadas na folha **Métricas do Redis** .

![Métricas do Redis][redis-cache-redis-metrics-blade]

> [!IMPORTANT]
> Se a mensagem a seguir for exibida na folha **Métricas do Redis** , siga as etapas na seção [Habilitar o diagnóstico de cache](#enable-cache-diagnostics) para habilitar o diagnóstico de cache.
> 
> `Monitoring may not be enabled. Click here to turn on Diagnostics.`
> 
> 

A folha **Métricas do Redis** tem gráficos de **Monitoramento** que exibem as métricas de cache. Cada gráfico pode ser personalizado com a adição ou remoção de métricas e a alteração do intervalo de relatório. Para exibir e configurar operações e alertas, a folha **Cache Redis** tem uma seção **Operações** que exibe **Eventos** e **Regras de alerta** de cache.

## <a name="enable-cache-diagnostics"></a>Habilitar o diagnóstico de cache
O Cache Redis do Azure fornece a capacidade de ter dados de diagnóstico armazenados em uma conta de armazenamento para que você possa usar as ferramentas desejadas para acessar e processar os dados diretamente. Para que o diagnóstico de cache seja coletado, armazenado e exibido no portal do Azure, uma conta de armazenamento deve ser configurada. Os caches na mesma região e assinatura compartilham a mesma conta de armazenamento de diagnóstico e, quando a configuração é alterada, se aplica a todos os caches na assinatura que estão nessa região.

Para habilitar e configurar o diagnóstico de cache, navegue até a folha do **Cache Redis** de sua instância de cache. Se o diagnóstico ainda não estiver habilitado, uma mensagem será exibida em vez de um gráfico de diagnóstico.

![Habilitar o diagnóstico de cache][redis-cache-enable-diagnostics]

Clique na mensagem para exibir a folha **Métrica** e clique em **Configurações de diagnóstico** para habilitar e configurar as configurações de diagnóstico para a instância do serviço de cache.

![Configurações de diagnóstico][redis-cache-diagnostic-settings]

![Configurar o diagnóstico][redis-cache-configure-diagnostics]

Clique no botão **Ativado** para habilitar o diagnóstico de cache e exibir a configuração de diagnóstico.

Clique na seta à direita de **Conta de Armazenamento** para selecionar uma conta de armazenamento para armazenar dados de diagnóstico. Para obter o melhor desempenho, selecione uma conta de armazenamento na mesma região que seu cache.

Quando as configurações de diagnóstico estiverem definidas, clique em **Salvar** para salvar a configuração. Observe que pode levar alguns minutos para que as alterações entrem em vigor.

> [!IMPORTANT]
> Os caches na mesma região e assinatura compartilham as mesmas configurações de armazenamento de diagnóstico e, quando a configuração é alterada (diagnóstico habilitado/desabilitado ou alteração da conta de armazenamento), ela é aplicada a todos os caches na assinatura que estão nessa região.
> 
> 

Para exibir as métricas armazenadas, examine as tabelas em sua conta de armazenamento com nomes que começam com `WADMetrics`. Para obter mais informações sobre como acessar as métricas armazenadas fora do portal do Azure, consulte o exemplo [Acessar dados de monitoramento do Cache Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

> [!NOTE]
> Apenas as métricas que estão armazenadas na conta de armazenamento selecionada são exibidas no portal do Azure. Se você alterar contas de armazenamento, os dados na conta de armazenamento configurada anteriormente permanecerão disponíveis para download, mas não serão exibidos no portal do Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de relatórios
As métricas de cache são relatadas usando vários intervalos de relatórios, incluindo **Última hora**, **Hoje**, **Semana passada** e **Personalizado**. A folha **Métrica** para cada gráfico de métricas exibe os valores médio, mínimo e máximo para cada métrica no gráfico, e algumas métricas exibem um total para o intervalo de relatório. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho de todo o cache, e para os caches que usam [cluster](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no nome mede o desempenho de um único fragmento em um cache. Por exemplo, se um cache tiver quatro fragmentos, `Cache Hits` será a quantidade total de ocorrências para todo o cache, e `Cache Hits (Shard 3)` será apenas as ocorrências para esse fragmento do cache.

> [!NOTE]
> Mesmo quando o cache está ocioso sem aplicativos de cliente ativos conectados, você pode ver alguma atividade no cache, como clientes conectados, uso de memória e operações que estão sendo executadas. Essa atividade é normal durante a operação de uma instância do Cache Redis do Azure.
> 
> 

| Métrica | Descrição |
| --- | --- |
| Acertos do Cache |O número de pesquisas de chave com êxito durante o intervalo de relatório especificado. Esse número é mapeado para `keyspace_hits` do comando [INFO](http://redis.io/commands/info) do Redis. |
| Erros de Cache |O número de pesquisas de chave com falha durante o intervalo de relatório especificado. Esse número é mapeado para `keyspace_misses` do comando INFO do Redis. Erros de cache não significam necessariamente que há um problema com o cache. Por exemplo, ao se usar o padrão de programação cache-aside, um aplicativo procura um item no cache primeiro. Se o item não estiver lá (erro de cache), o item será recuperado do banco de dados e adicionado ao cache na próxima vez. Erros de cache são o comportamento normal para o padrão de programação cache-aside. Se o número de erros de cache for maior do que o esperado, examine a lógica do aplicativo que popula e lê do cache. Se os itens estiverem sendo removidos do cache devido à pressão de memória, poderá haver alguns erros de cache, mas uma métrica melhor para monitorar a pressão de memória seria `Used Memory` ou `Evicted Keys`. |
| Clientes conectados |O número de conexões de cliente com o cache durante o intervalo de relatório especificado. Esse número é mapeado para `connected_clients` do comando INFO do Redis. Assim que o [limite de conexão](cache-configure.md#default-redis-server-configuration) for atingido, as tentativas de conexão subsequentes ao cache falharão. Observe que, mesmo que não haja um aplicativo cliente ativo, pode haver ainda algumas instâncias de clientes conectados devido a conexões e processos internos. |
| Chaves removidas |O número de itens removidos do cache durante o intervalo de relatório especificado devido ao limite de `maxmemory` . Esse número é mapeado para `evicted_keys` do comando INFO do Redis. |
| Chaves expiradas |O número de itens expirados do cache durante o intervalo de relatório especificado. Esse valor é mapeado para `expired_keys` do comando INFO do Redis. |
| Gets |O número de operações get do cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores de informações do comando INFO all do Redis: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` e `cmdstat_getrange`. Ele é equivalente à soma de acertos e erros de cache durante o intervalo de relatório. |
| Carga do Servidor Redis |O percentual de ciclos em que o servidor Redis está ocupado processando, em vez de ocioso esperando por mensagens. Se esse contador atingir 100, isso indicará que o servidor Redis atingiu um limite de desempenho, e a CPU não pode processar o trabalho mais depressa. Se houver alta Carga do Servidor Redis, haverá exceções de tempo limite no cliente. Nesse caso, considere escalar verticalmente ou particionar seus dados em vários caches. |
| Sets |O número de operações set para o cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores de informações do comando INFO all do Redis : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` e `cmdstat_setnx`. |
| Total de Operações |O número total de comandos processados pelo servidor de cache durante o intervalo de relatório especificado. Esse valor é mapeado para `total_commands_processed` do comando INFO do Redis. Observe que, quando o Cache Redis do Azure for usado somente para publicação/assinatura, não haverá métricas para `Cache Hits`, `Cache Misses`, `Gets` ou `Sets`, mas haverá métricas de `Total Operations` que refletem o uso do cache para operações de publicação/assinatura. |
| Memória Usada |A quantidade de memória de cache usada para os pares de chave/valor no cache, em MB, durante o intervalo de relatório especificado. Esse valor é mapeado para `used_memory` do comando INFO do Redis. Isso não inclui metadados ou fragmentação. |
| Memória RSS Usada |A quantidade de memória de cache usada, em MB, durante o intervalo de relatório especificado, incluindo fragmentação e metadados. Esse valor é mapeado para `used_memory_rss` do comando INFO do Redis. |
| CPU |A utilização da CPU do servidor do Cache Redis do Azure como um percentual durante o intervalo de relatório especificado. Esse valor é mapeado para o contador de desempenho `\Processor(_Total)\% Processor Time` do sistema operacional. |
| Cache Lido |A quantidade de dados lidos do cache, em MB/s, durante o intervalo de relatório especificado. Esse valor é derivado das placas de adaptador de rede que dão suporte à máquina virtual que hospeda o cache e não é específico do Redis. **Esse valor corresponde à largura de banda da rede usada por esse cache. Se você deseja configurar alertas para limites de largura de banda de rede no servidor, crie-os usando o contador `Cache Read`. Confira [esta tabela](cache-faq.md#cache-performance) para ver os limites de largura de banda observados para vários tamanhos e tipos de preço de cache.** |
| Gravação no Cache |A quantidade de dados gravados no cache, em MB/s, durante o intervalo de relatório especificado. Esse valor é derivado das placas de adaptador de rede que dão suporte à máquina virtual que hospeda o cache e não é específico do Redis. Esse valor corresponde à largura de banda de rede de dados enviados para o cache do cliente. |

## <a name="how-to-view-metrics-and-customize-charts"></a>Como exibir métricas e personalizar gráficos
Você pode exibir uma visão geral das métricas para o cache na folha **Métricas do Redis** . Para acessar a folha **Métricas do Redis**, escolha **Todas as configurações** > **Métricas do Redis**.

![Métricas do Redis][redis-cache-redis-metrics]

A folha **Métricas do Redis** contém os gráficos a seguir.

| Gráfico de métricas do Redis | Métricas exibidas |
| --- | --- |
| Leitura de cache e Gravação em cache |Cache Lido |
| Gravação no Cache | |
| Clientes conectados |Clientes conectados |
| Acertos e Erros |Acertos do Cache |
| Erros de Cache | |
| Total de Comandos |Total de Operações |
| Gets e Sets |Gets |
| Sets | |
| Uso da CPU |CPU |
| Uso de Memória |Memória Usada |
| Memória RSS Usada | |
| Carga do Servidor Redis |Carga do Servidor |
| Contagem de chaves |Total de chaves |
| Chaves removidas | |
| Chaves expiradas | |

Para obter uma visão mais detalhada das métricas em um gráfico específico e personalizar o gráfico, clique no gráfico desejado na folha **Métricas do Redis** para exibir a folha **Métrica** desse gráfico.

![Lâmina Métrica][redis-cache-metric-blade]

Todos os alertas definidos nas métricas exibidas por um gráfico são listados na parte inferior da folha **Métrica** do gráfico.

Para adicionar ou remover métricas ou alterar o intervalo de relatório, escolha **Editar Gráfico**.

Para adicionar ou remover métricas do gráfico, clique na caixa de seleção ao lado do nome da métrica. Para alterar o intervalo de relatório, clique no intervalo desejado. Para alterar o **Tipo de gráfico**, clique no estilo desejado. Depois que as alterações desejadas forem feitas, clique em **Salvar**. 

![Editar Gráfico][redis-cache-edit-chart]

Quando você clicar em **Salvar**, suas alterações persistirão até que você saia da folha **Métrica**. Quando voltar mais tarde, você verá a métrica e o intervalo de tempo originais novamente. Para obter mais informações sobre como personalizar gráficos, consulte [Monitorar métricas de serviço](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para exibir as métricas de determinado período de tempo em um gráfico, passe o mouse sobre um dos pontos ou barras específicos no gráfico que correspondem ao período de tempo desejado, e as métricas desse intervalo serão exibidas.

![Exibir detalhes do gráfico][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Como monitorar um cache premium com clustering
Os caches premium têm [clustering](cache-how-to-premium-clustering.md) habilitado pode ter até 10 fragmentos. Cada fragmento tem suas próprias métricas e essas métricas agregadas para fornecer métricas de cache como um todo. Cada métrica inclui duas versões. Uma métrica mede o desempenho de todo o cache e uma segunda versão da métrica que inclui `(Shard 0-9)` no desempenho de medidas do nome para um fragmento único em um cache. Por exemplo, se um cache tiver três fragmentos, `Cache Hits` será a quantidade total de ocorrências para todo o cache,`Cache Hits (Shard 2)` e será apenas as ocorrências para esse fragmento do cache.

Cada gráfico de monitoramento exibe as métricas de nível superior para o cache com as métricas para cada fragmento de cache.

![Monitoramento][redis-cache-premium-monitor]

Passar o mouse sobre os pontos de dados exibe os detalhes para esse ponto no tempo. 

![Monitoramento][redis-cache-premium-point-summary]

Os valores maiores normalmente são os valores agregados ao cache enquanto os menores valores são as métricas individuais para o fragmento. Observe que neste exemplo há três fragmentos e os acertos de cache são distribuídos uniformemente entre os fragmentos.

![Monitoramento][redis-cache-premium-point-shard]

Para ver mais detalhes, clique no gráfico para exibir uma exibição expandida na folha **métrica** .

![Monitoramento][redis-cache-premium-chart-detail]

Por padrão, cada gráfico inclui o contador de desempenho do cache de nível superior, bem como os contadores de desempenho para os fragmentos individuais. Você pode personalizá-los na folha **Editar gráfico** .

![Monitoramento][redis-cache-premium-edit]

Para obter mais informações sobre contadores de desempenho disponíveis, consulte as [Métricas disponíveis e intervalos de relatório](#available-metrics-and-reporting-intervals).

## <a name="operations-and-alerts"></a>Operações e alertas
A seção **Operações** na folha **Cache Redis** tem as seções **Eventos** e **Regras de alerta**.

![Operações][redis-cache-operations-events]

Para ver uma lista das operações de cache recentes, clique no gráfico **Eventos** para exibir a folha **Eventos**. Exemplos de operações incluem a recuperação e a regeneração de chaves de acesso e a ativação e a resolução de regras de alerta. Para obter mais informações sobre cada evento, clique no evento na folha **Eventos** .

Para saber mais sobre eventos, confira [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md).

A seção **Regras de alerta** exibe a contagem de alertas para a instância de cache. Uma regra de alerta o habilita a monitorar sua instância de cache e receber um email sempre que determinado valor de métrica atinge o limite definido na regra. 

Regras de alerta são avaliadas aproximadamente a cada cinco minutos e, quando uma regra de alerta é ativada, as notificações configuradas são enviadas. As notificações e ativações de regras de alerta não são processadas instantaneamente. Pode haver um atraso de alguns minutos antes que uma regra de alerta seja ativada e as notificações sejam enviadas.

As regras de alerta podem ser exibidas e definidas na folha **Métrica** de um gráfico de monitoramento específico ou na folha **Regras de alerta**.

As regras de alerta têm as propriedades a seguir.

| Propriedade de regra de alerta | Descrição |
| --- | --- |
| Recurso |O recurso avaliado pela regra de alerta. Ao se criar uma regra de alerta de um cache Redis, o cache é o recurso. |
| Nome |Nome que identifica com exclusividade a regra de alerta na instância atual do cache. |
| Descrição |Descrição opcional da regra de alerta. |
| Métrica |A métrica a ser monitorada pela regra de alerta. Para obter uma lista de métricas de cache, consulte Métricas disponíveis e intervalos de relatório. |
| Condição |O operador de condição da regra de alerta. As opções possíveis são: maior que, maior ou igual a, menor que, menor ou igual a |
| Limite |O valor usado para comparar à métrica usando o operador especificado pela propriedade de condição. Dependendo da métrica, esse valor pode ser em bytes/segundo, bytes, percentual ou contagem. |
| Período |Especifica o período em que o valor médio da métrica é usado para a comparação de regra de alerta. Por exemplo, se o período for a última hora, o valor médio da métrica durante o intervalo da hora anterior será usado para a comparação. Se você quiser ser notificado quando o limite for atingido devido a um aumento de atividade, um período mais curto será apropriado. Para ser notificado quando houver atividade mantida acima do limite, use um período mais longo. |
| Serviço de email e coadministradores |Se for verdadeiro, o administrador de serviço e o coadministrador receberão um email quando o alerta for ativado. |
| Email de administrador adicional |Endereço de email opcional de um administrador adicional a ser notificado quando o alerta for ativado. |

Apenas uma notificação é enviada por ativação de regra de alerta. Depois que o limite de uma regra é excedido e uma notificação é enviada, a regra não é avaliada novamente até que a métrica esteja abaixo do limite. Se a métrica subsequentemente exceder o limite, o alerta será reativado, e uma nova notificação será enviada.

Para exibir todas as regras de alerta da instância de cache, clique na parte **Regras de alerta** da folha **Cache Redis**. Para exibir apenas as regras de alerta que usam uma métrica específica, navegue até a folha **Métrica** do gráfico que contém a métrica.

![Regras de alerta][redis-cache-alert-rules]

Para adicionar uma regra de alerta, clique em **Adicionar alerta** na folha **Métrica** ou na folha **Regras de alerta**. 

Digite os critérios de regra desejados para a folha de regra **Adicionar um alerta** e clique em **OK**. 

![Adicionar regra de alerta][redis-cache-add-alert]

> [!NOTE]
> Quando uma regra de alerta é criada clicando-se em **Adicionar alerta** na folha **Métrica**, apenas as métricas exibidas no gráfico nessa folha aparecem na lista suspensa **Métrica**. Quando uma regra de alerta é criada clicando-se em **Adicionar alerta** na folha **Regras de alerta**, todas as métricas de cache estão disponíveis na lista suspensa **Métrica**.
> 
> 

Quando uma regra de alerta é salva, ela aparece na folha **Regras de alerta**, bem como na folha **Métrica** dos gráficos que exibem a métrica usada na regra de alerta. Para editar uma regra de alerta, clique no nome da regra de alerta para exibir a folha **Editar Regra** . Na folha **Editar Regra** , você pode editar as propriedades da regra, excluir ou desabilitar a regra de alerta ou reabilitar a regra se ela tiver sido desabilitada anteriormente.

> [!NOTE]
> As alterações feitas nas propriedades da regra podem levar alguns instantes para serem refletidas na folha **Regras de alerta** ou na folha **Métrica**.
> 
> 

Quando uma regra de alerta é ativada, um email é enviado, dependendo da configuração da regra de alerta, e um ícone de alerta é exibido na parte **Regras de alerta** da folha **Cache Redis**.

Uma regra de alerta é considerada resolvida quando a condição de alerta não é mais avaliada como verdadeira. Quando a condição de regra de alerta é resolvida, o ícone de alerta é substituído por uma marca de seleção. Para obter detalhes sobre ativações de alertas e resoluções, clique na parte **Eventos** na folha **Cache Redis** para exibir os eventos na folha **Eventos**.

Para saber mais sobre os alertas no Azure, confira [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Métricas na folha Cache Redis
A folha **Cache Redis** exibe as categorias de métricas a seguir.

* [Gráficos de monitoramento](#monitoring-charts)
* [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitoramento
A seção **Monitoramento** tem gráficos de **Acertos e Erros**, **Gets e Sets**, **Conexões** e **Total de Comandos**.

![Gráficos de monitoramento][redis-cache-monitoring-part]

Os gráficos de **Monitoramento** exibem as métricas a seguir.

| Gráfico de monitoramento | Métricas de cache |
| --- | --- |
| Acertos e Erros |Acertos do Cache |
| Erros de Cache | |
| Gets e Sets |Gets |
| Sets | |
| Conexões |Clientes conectados |
| Total de Comandos |Total de Operações |

Para obter informações sobre como exibir as métricas e personalizar os gráficos individuais nesta seção, consulte a seção a seguir, [Como exibir métricas e personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts) .

### <a name="usage-charts"></a>Gráficos de uso
A seção **Uso** tem gráficos de **Carga do Servidor Redis**, **Uso de Memória**, **Largura de Banda de Rede** e **Uso de CPU** e também exibe ao**Tipo de preço** para a instância de cache.

![Gráficos de uso][redis-cache-usage-part]

A **Tipo de preço** exibe a tipo de preço do cache e pode ser usada para [dimensionar](cache-how-to-scale.md) o cache para um tipo de preço diferente.

Os gráficos de **Uso** exibem as métricas a seguir.

| Gráfico de uso | Métricas de cache |
| --- | --- |
| Carga do Servidor Redis |Carga do Servidor |
| Uso de Memória |Memória Usada |
| Largura de Banda de Rede |Gravação no Cache |
| Uso da CPU |CPU |

Para obter informações sobre como exibir as métricas e personalizar os gráficos individuais nesta seção, consulte a seção a seguir, [Como exibir métricas e personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts) .

<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png






<!--HONumber=Nov16_HO3-->


