<properties 
	pageTitle="Como monitorar o Cache Redis do Azure" 
	description="Saiba como monitorar a integridade e o desempenho de suas instâncias do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="sdanie"/>

# Como monitorar o Cache Redis do Azure

O Cache Redis do Azure fornece várias opções para monitorar suas instâncias de cache. Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem monitorar a integridade de suas instâncias de Cache Redis do Azure e ajudá-lo a gerenciar seus aplicativos de cache.

Quando o diagnóstico de cache é habilitado, métricas para instâncias do Cache Redis do Azure são coletadas aproximadamente a cada 30 segundos e armazenadas para que possam ser exibidas nos gráficos de métricas e avaliadas por regras de alerta.

As métricas de cache são coletadas usando o comando [INFO](http://redis.io/commands/info) do Redis. Para obter mais informações sobre os diferentes comandos INFO usados para cada métrica de cache, consulte [Métricas disponíveis e intervalos de relatórios](#available-metrics-and-reporting-intervals).

Para exibir as métricas de cache [procure](https://msdn.microsoft.com/library/azure/cbe6d113-7bdc-4664-a59d-ff0df6f4e214#CacheSettings) sua instância de cache no [portal do Azure](https://portal.azure.com). As métricas para instâncias do Cache Redis do Azure são acessadas na folha do **Cache Redis**.

![Monitoramento][redis-cache-monitor-overview]

>[AZURE.IMPORTANT]Se a mensagem a seguir for exibida no portal do Azure, siga as etapas na seção [Habilitar o diagnóstico de cache](#enable-cache-diagnostics) para habilitar o diagnóstico de cache.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

A folha do **Cache Redis** tem gráficos de **Monitoramento** e **Uso** que exibem as métricas de cache. Cada gráfico pode ser personalizado com a adição ou remoção de métricas e a alteração do intervalo de relatório. A folha do **Cache Redis** também tem uma seção **Operações** que exibe os **Eventos** de cache e as **Regras de alerta**.

## Habilitar o diagnóstico de cache

O Cache Redis do Azure fornece a capacidade de ter dados de diagnóstico armazenados em uma conta de armazenamento para que você possa usar as ferramentas desejadas para acessar e processar os dados diretamente. Para que o diagnóstico de cache seja coletado, armazenado e exibido no portal de visualização do Azure, uma conta de armazenamento deve ser configurada. Os caches na mesma região e assinatura compartilham a mesma conta de armazenamento de diagnóstico e, quando a configuração é alterada, se aplica a todos os caches na assinatura que estão nessa região.

Para habilitar e configurar o diagnóstico de cache, navegue até a folha do **Cache Redis** de sua instância de cache. Se o diagnóstico ainda não estiver habilitado, uma mensagem será exibida em vez de um gráfico de diagnóstico.

![Habilitar o diagnóstico de cache][redis-cache-enable-diagnostics]

Clique em um dos gráficos de monitoramento, como **Acertos e Erros**, para exibir a folha **Métrica** e clique em **Configurações de diagnóstico** para habilitar e configurar as configurações de diagnóstico para a instância do serviço de cache.

![Configurações de diagnóstico][redis-cache-diagnostic-settings]

![Configurar o diagnóstico][redis-cache-configure-diagnostics]

Clique no botão **Ativado** para habilitar o diagnóstico de cache e exibir a configuração de diagnóstico.

Clique na seta à direita de **Conta de Armazenamento** para selecionar uma conta de armazenamento para armazenar dados de diagnóstico. Para obter o melhor desempenho, selecione uma conta de armazenamento na mesma região que seu cache.

Use a lista suspensa **Retenção (dias)** para selecionar o período de retenção para os dados de diagnóstico. Você também pode digitar o número de dias desejado na caixa de texto na parte superior da lista.

Quando as configurações de diagnóstico estiverem definidas, clique em **Salvar** para salvar a configuração. Observe que pode levar alguns minutos para que as alterações entrem em vigor.

>[AZURE.IMPORTANT]Os caches na mesma região e assinatura compartilham a mesma conta de armazenamento de diagnóstico e, quando a configuração é alterada, se aplica a todos os caches na assinatura que estão nessa região.

Para exibir as métricas armazenadas, examine as tabelas em sua conta de armazenamento com nomes que começam com `WADMetrics`. Para obter mais informações sobre como acessar as métricas armazenadas fora do portal do Azure, consulte o exemplo [Acessar dados de monitoramento do Cache Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).

>[AZURE.NOTE]Apenas as métricas que estão armazenadas na conta de armazenamento selecionada são exibidas no portal. Se você alterar contas de armazenamento, os dados na conta de armazenamento configurada anteriormente permanecerão disponíveis para download, mas não serão exibidos no portal e não serão removidos quando o intervalo do período de retenção expirar.

## Métricas disponíveis e intervalos de relatórios

As métricas de cache são relatadas usando vários intervalos de relatórios, incluindo **Última hora**, **Hoje**, **Semana passada** e **Personalizado**. A folha **Métrica** para cada gráfico de métricas exibe os valores médio, mínimo e máximo para cada métrica no gráfico, e algumas métricas exibem um total para o intervalo de relatório.

>[AZURE.NOTE]Mesmo quando o cache está ocioso sem aplicativos de cliente ativos conectados, você pode ver alguma atividade no cache, como clientes conectados, uso de memória e operações que estão sendo executadas. Essa atividade é normal durante a operação de uma instância do Cache Redis do Azure.

| Métrica | Descrição |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Acertos do Cache | O número de pesquisas de chave com êxito durante o intervalo de relatório especificado. Esse valor é mapeado para o INFO `keyspace_hits command` do Redis. |
| Erros de Cache | O número de pesquisas de chave com falha durante o intervalo de relatório especificado. Esse valor é mapeado para o comando INFO `keyspace_misses` do Redis. Erros de cache não significam necessariamente que há um problema com o cache. Por exemplo, ao se usar o padrão de programação cache-aside, um aplicativo procura um item no cache primeiro. Se o item não estiver lá (erro de cache), o item será recuperado do banco de dados e adicionado ao cache na próxima vez. Erros de cache são o comportamento normal para o padrão de programação cache-aside. Se o número de erros de cache for maior do que o esperado, examine a lógica do aplicativo que popula e lê do cache. Se os itens estiverem sendo removidos do cache devido à pressão de memória, poderá haver alguns erros de cache, mas uma métrica melhor para monitorar a pressão de memória seria `Used Memory` ou `Evicted Keys`. |
| Clientes conectados | O número de conexões de cliente com o cache durante o intervalo de relatório especificado. Esse valor é mapeado para o comando INFO `connected_clients` do Redis. O limite de clientes conectados é 10.000 e, quando ele é atingido, as tentativas de conexão subsequentes ao cache falham. Observe que, mesmo que não haja um aplicativo cliente ativo, pode haver ainda algumas instâncias de clientes conectados devido a conexões e processos internos. |
| Chaves removidas | O número de itens removidos do cache durante o intervalo de relatório especificado devido ao limite de `maxmemory`. Esse valor é mapeado para o comando INFO `evicted_keys` do Redis. |
| Chaves expiradas | O número de itens expirados do cache durante o intervalo de relatório especificado. Esse valor é mapeado para o comando INFO `expired_keys` do Redis. |
| Gets | O número de operações get do cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores de informações do comando INFO all do Redis: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` e `cmdstat_getrange`. Ele é equivalente à soma de acertos e erros de cache durante o intervalo de relatório. |
| Carga do Servidor Redis | O percentual de ciclos em que o servidor Redis está ocupado processando, em vez de ocioso esperando por mensagens. Se esse contador atingir 100, isso indicará que o servidor Redis atingiu um limite de desempenho, e a CPU não pode processar o trabalho mais depressa. Se houver alta Carga do Servidor Redis, haverá exceções de tempo limite no cliente. Nesse caso, considere escalar verticalmente ou particionar seus dados em vários caches. |
| Sets | O número de operações set para o cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores de informações do comando INFO all do Redis : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` e `cmdstat_setnx`. |
| Total de Operações | O número total de comandos processados pelo servidor de cache durante o intervalo de relatório especificado. Esse valor é mapeado para o comando INFO `total_commands_processed` do Redis. Observe que, quando o Cache Redis do Azure for usado somente para publicação/assinatura, não haverá métricas para `Cache Hits`, `Cache Misses`, `Gets` ou `Sets`, mas haverá métricas de `Total Operations` que refletem o uso do cache para operações de publicação/assinatura. |
| Memória Usada | A quantidade de memória de cache usada, em MB, durante o intervalo de relatório especificado. Esse valor é mapeado para o comando INFO `used_memory` do Redis. |
| CPU | A utilização da CPU do servidor do Cache Redis do Azure como um percentual durante o intervalo de relatório especificado. Esse valor é mapeado para o contador de desempenho `\Processor(_Total)\% Processor Time` do sistema operacional. |
| Cache Lido | A quantidade de dados lidos do cache, em KB/s, durante o intervalo de relatório especificado. Esse valor é derivado das placas de interface de rede que dão suporte à máquina virtual que hospeda o cache e não é específico do Redis. |
| Gravação no Cache | A quantidade de dados gravados no cache, em KB/s, durante o intervalo de relatório especificado. Esse valor é derivado das placas de interface de rede que dão suporte à máquina virtual que hospeda o cache e não é específico do Redis. |

## Gráficos de monitoramento

A seção **Monitoramento** tem gráficos de **Acertos e Erros**, **Gets e Sets**, **Conexões** e **Total de Comandos**.

![Gráficos de monitoramento][redis-cache-monitoring-part]

Os gráficos de **Monitoramento** exibem as métricas a seguir.

| Gráfico de monitoramento | Métricas de cache |
|------------------|-------------------|
| Acertos e Erros | Acertos do Cache |
| | Erros de Cache |
| Gets e Sets | Gets |
| | Sets |
| Conexões | Clientes conectados |
| Total de Comandos | Total de Operações |

Para obter informações sobre como exibir as métricas e personalizar os gráficos individuais nesta seção, consulte a seção a seguir, [Como exibir métricas e personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts).

## Gráficos de uso

A seção **Uso** tem gráficos de **Carga do Servidor Redis**, **Uso de Memória**, **Largura de Banda de Rede** e **Uso de CPU** e também exibe a **Camada de preços** para a instância de cache.

![Gráficos de uso][redis-cache-usage-part]

A **Camada de preços** exibe a camada de preços do cache e pode ser usada para [dimensionar](cache-how-to-scale.md) o cache para uma camada de preços diferente.

Os gráficos de **Uso** exibem as métricas a seguir.

| Gráfico de uso | Métricas de cache |
|-------------------|---------------|
| Carga do Servidor Redis | Carga do Servidor |
| Uso de Memória | Memória Usada |
| Largura de Banda de Rede | Gravação no Cache |
| Uso da CPU | CPU |

Para obter informações sobre como exibir as métricas e personalizar os gráficos individuais nesta seção, consulte a seção a seguir, [Como exibir métricas e personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts).

## Como exibir métricas e personalizar gráficos

Você pode exibir uma visão geral das métricas na folha **Cache Redis** nos gráficos de **Monitoramento** e **Uso** conforme descrito nas seções anteriores. Para obter uma visão mais detalhada das métricas em um gráfico específico e para personalizar o gráfico, clique no gráfico desejado da folha **Cache Redis** para exibir a folha **Métrica** desse gráfico.

![Lâmina Métrica][redis-cache-metric-blade]

Todos os alertas definidos nas métricas exibidas por um gráfico são listados na parte inferior da folha **Métrica** do gráfico.

Para adicionar ou remover métricas ou alterar o intervalo de relatório, clique com o botão direito do mouse no gráfico e escolha **Editar Gráfico**. Também é possível editar gráficos diretamente na folha **Cache Redis** clicando no gráfico desejado e escolhendo **Editar Gráfico**.

Para adicionar ou remover métricas do gráfico, clique na caixa de seleção ao lado do nome da métrica. Para alterar o intervalo de relatório, clique no intervalo desejado. Para alterar o **Tipo de gráfico**, clique no estilo desejado. Depois que as alterações desejadas forem feitas, clique em **Salvar**.

![Editar gráfico][redis-cache-edit-chart]

Quando você clicar em **Salvar**, suas alterações persistirão até que você saia da folha **Métrica**. Quando voltar mais tarde, você verá a métrica e o intervalo de tempo originais novamente. Para obter mais informações sobre como personalizar gráficos, consulte [Monitorar métricas de serviço](../azure-portal/insights-how-to-customize-monitoring.md).

Para exibir as métricas de determinado período de tempo em um gráfico, passe o mouse sobre um dos pontos ou barras específicos no gráfico que correspondem ao período de tempo desejado, e as métricas desse intervalo serão exibidas.

![Exibir detalhes do gráfico][redis-cache-view-chart-details]

## Operações e alertas

A seção **Operações** tem as seções **Eventos** e **Regras de alerta**.

![Operações][redis-cache-operations-events]

Para ver uma lista das operações de cache recentes, clique no gráfico **Eventos** para exibir a folha **Eventos**. Exemplos de operações incluem a recuperação e a regeneração de chaves de acesso e a ativação e a resolução de regras de alerta. Para obter mais informações sobre cada evento, clique no evento na folha **Eventos**.

Para obter mais informações sobre eventos, consulte [Exibir eventos e logs de auditoria](../azure-portal/insights-debugging-with-events.md).

A seção **Regras de alerta** exibe a contagem de alertas para a instância de cache. Uma regra de alerta o habilita a monitorar sua instância de cache e receber um email sempre que determinado valor de métrica atinge o limite definido na regra.

Regras de alerta são avaliadas aproximadamente a cada cinco minutos e, quando uma regra de alerta é ativada, as notificações configuradas são enviadas. As notificações e ativações de regras de alerta não são processadas instantaneamente. Pode haver um atraso de alguns minutos antes que uma regra de alerta seja ativada e as notificações sejam enviadas.

As regras de alerta podem ser exibidas e definidas na folha **Métrica** de um gráfico de monitoramento específico ou na folha **Regras de alerta**.

As regras de alerta têm as propriedades a seguir.

| Propriedade de regra de alerta | Descrição |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Recurso | O recurso avaliado pela regra de alerta. Ao se criar uma regra de alerta de um cache Redis, o cache é o recurso. |
| Nome | Nome que identifica com exclusividade a regra de alerta na instância atual do cache. |
| Descrição | Descrição opcional da regra de alerta. |
| Métrica | A métrica a ser monitorada pela regra de alerta. Para obter uma lista de métricas de cache, consulte Métricas disponíveis e intervalos de relatório. |
| Condição | O operador de condição da regra de alerta. As opções possíveis são: maior que, maior ou igual a, menor que, menor ou igual a |
| Limite | O valor usado para comparar à métrica usando o operador especificado pela propriedade de condição. Dependendo da métrica, esse valor pode ser em bytes/segundo, bytes, percentual ou contagem. |
| Período | Especifica o período em que o valor médio da métrica é usado para a comparação de regra de alerta. Por exemplo, se o período for a última hora, o valor médio da métrica durante o intervalo da hora anterior será usado para a comparação. Se você quiser ser notificado quando o limite for atingido devido a um aumento de atividade, um período mais curto será apropriado. Para ser notificado quando houver atividade mantida acima do limite, use um período mais longo. |
| Serviço de email e coadministradores | Se for verdadeiro, o administrador de serviço e o coadministrador receberão um email quando o alerta for ativado. |
| Email de administrador adicional | Endereço de email opcional de um administrador adicional a ser notificado quando o alerta for ativado. |

Apenas uma notificação é enviada por ativação de regra de alerta. Depois que o limite de uma regra é excedido e uma notificação é enviada, a regra não é avaliada novamente até que a métrica esteja abaixo do limite. Se a métrica subsequentemente exceder o limite, o alerta será reativado, e uma nova notificação será enviada.

Para exibir todas as regras de alerta da instância de cache, clique na parte **Regras de alerta** da folha do **Cache Redis**. Para exibir apenas as regras de alerta que usam uma métrica específica, navegue até a folha **Métrica** do gráfico que contém a métrica.

![Regras de alerta][redis-cache-alert-rules]

Para adicionar uma regra de alerta, clique em **Adicionar alerta** na folha **Métrica** ou na folha **Regras de alerta**.

Digite os critérios de regra desejados para a folha de regra **Adicionar um alerta** e clique em **OK**.

![Adicionar regra de alerta][redis-cache-add-alert]

>[AZURE.NOTE]Quando uma regra de alerta é criada clicando-se em **Adicionar alerta** na folha **Métrica**, apenas as métricas exibidas no gráfico nessa folha aparecem na lista suspensa **Métrica**. Quando uma regra de alerta é criada clicando-se em **Adicionar alerta** na folha **Regras de alerta**, todas as métricas de cache estão disponíveis na lista suspensa **Métrica**.

Quando uma regra de alerta é salva, ela aparece na folha **Regras de alerta**, bem como na folha **Métrica** dos gráficos que exibem a métrica usada na regra de alerta. Para editar uma regra de alerta, clique no nome da regra de alerta para exibir a folha **Editar Regra**. Na folha **Editar Regra**, você pode editar as propriedades da regra, excluir ou desabilitar a regra de alerta ou reabilitar a regra se ela tiver sido desabilitada anteriormente.

>[AZURE.NOTE]As alterações feitas nas propriedades da regra podem levar alguns instantes para serem refletidas na folha **Regras de alerta** ou na folha **Métrica**.

Quando uma regra de alerta é ativada, um email é enviado, dependendo da configuração da regra de alerta, e um ícone de alerta é exibido na parte **Regras de alerta** da folha do **Cache Redis**.

Uma regra de alerta é considerada resolvida quando a condição de alerta não é mais avaliada como verdadeira. Quando a condição de regra de alerta é resolvida, o ícone de alerta é substituído por uma marca de seleção. Para obter detalhes sobre ativações de alertas e resoluções, clique na parte **Eventos** na folha do **Cache Redis** para exibir os eventos na folha **Eventos**.

Para obter mais informações sobre os alertas no Azure, consulte [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md).
  
<!-- IMAGES -->
[redis-cache-monitor-overview]: ./media/cache-how-to-monitor/redis-cache-monitor-overview.png

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

<!---HONumber=August15_HO6-->