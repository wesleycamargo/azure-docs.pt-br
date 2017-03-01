---
title: Como configurar o Cache Redis do Azure | Microsoft Docs
description: "Entenda a configuração padrão Redis Cache Redis do Azure e aprenda a configurar as instâncias de Cache Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 296a842157e4ecae1b3700e1d22c56852ffc06a2
ms.openlocfilehash: ad88d998302d264b2a0f607d8408cd12bf833a68
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-configure-azure-redis-cache"></a>Como configurar o Cache Redis do Azure
Este tópico descreve como examinar e atualizar a configuração para suas instâncias de Cache Redis do Azure, além de incluir a configuração do servidor Redis padrão para instâncias de Cache Redis do Azure.

> [!NOTE]
> Para obter mais informações sobre como configurar e usar os recursos de cache premium, consulte [Como configurar a persistência](cache-how-to-premium-persistence.md), [Como configurar o clustering](cache-how-to-premium-clustering.md) e [Como configurar o suporte à Rede Virtual](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Definir configurações de cache Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

As configurações de Cache Redis do Azure são exibidas e configuradas na folha **Cache Redis** usando o **Menu Recursos**.

![Configurações de Cache Redis](./media/cache-configure/redis-cache-settings.png)

Você pode exibir e definir as seguintes configurações usando o **Menu recursos**.

* [Visão geral](#overview)
* [Log de atividade](#activity-log)
* [Controle de acesso (IAM)](#access-control-iam)
* [Marcas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Configurações](#settings)
    * [Chaves de acesso](#access-keys)
    * [Configurações avançadas](#advanced-settings)
    * [Supervisor do Cache Redis](#redis-cache-advisor)
    * [Tipo de preços](#pricing-tier)
    * [Tamanho do cluster Redis](#cluster-size)
    * [Persistência de dados do Redis](#redis-data-persistence)
    * [Agendar atualizações](#schedule-updates)
    * [Rede Virtual](#virtual-network)
    * [Propriedades](#properties)
    * [Bloqueios](#locks)
    * [Script de automação](#automation-script)
* [Administração](#administration)
    * [Importar dados](#importexport)
    * [Exportar Dados](#importexport)
    * [Reboot](#reboot)
* [Monitoramento](#monitoring)
    * [Métricas do Redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnostics](#diagnostics)
* [Configurações de suporte e solução de problemas](#support-amp-troubleshooting-settings)
    * [Integridade de recursos](#resource-health)
    * [Nova solicitação de suporte](#new-support-request)


## <a name="overview"></a>Visão geral

**A visão geral** fornece informações básicas sobre o cache como: nome, portas, tipo de preço e métricas de cache selecionadas.

### <a name="activity-log"></a>Log de atividades

Clique em **Log de auditoria** para exibir as ações executadas em seu cache. Você também pode usar a filtragem para expandir essa exibição a fim de incluir outros recursos. Para saber mais sobre como trabalhar com logs de auditoria, confira [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) e [Operações de auditoria com o Resource Manager](../azure-resource-manager/resource-group-audit.md). Para saber mais sobre como monitorar eventos do Cache Redis do Azure, confira [Operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controle de acesso (IAM)

A seção **Controle de acesso (IAM)** dá suporte ao RBAC (controle de acesso baseado em função) no Portal do Azure para ajudar as organizações a atender aos seus requisitos de gerenciamento de acesso de maneira simples e precisa. Para saber mais, confira [Role-based access control in the Azure portal](../active-directory/role-based-access-control-configure.md)(Controle de acesso baseado em função no portal do Azure).

### <a name="tags"></a>Marcas

A seção **Marcas** o ajuda a organizar seus recursos. Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **Diagnosticar e solucionar problemas** para ver problemas comuns e estratégias para resolvê-los.



## <a name="settings"></a>Configurações
A seção **Configurações** permite acessar e definir as seguintes configurações para seu cache.

![Configurações](./media/cache-configure/redis-cache-general-settings.png)

* [Chaves de acesso](#access-keys)
* [Configurações avançadas](#advanced-settings)
* [Supervisor do Cache Redis](#redis-cache-advisor)
* [Tipo de preços](#pricing-tier)
* [Tamanho do cluster Redis](#cluster-size)
* [Persistência de dados do Redis](#redis-data-persistence)
* [Agendar atualizações](#schedule-updates)
* [Rede Virtual](#virtual-network)
* [Propriedades](#properties)
* [Bloqueios](#locks)
* [Script de automação](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **Teclas de acesso** para exibir ou regenerar as teclas de acesso para seu cache. Essas chaves são usadas pelos clientes que se conectam ao seu cache.

![Chaves de acesso de Cache Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Configurações avançadas
As configurações a seguir são definidas na folha **Configurações avançadas**.

* [Portas de acesso](#access-ports)
* [Maxmemory-policy e maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)
* [Notificações de Keyspace (configurações avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de acesso
Por padrão, o acesso não SSL está desabilitado para novos caches. Para habilitar a porta não SSL, clique em **Não** para **Permitir acesso somente via SSL** na folha **Configurações avançadas** e, em seguida, clique em **Salvar**.

![Portas de acesso de Cache Redis](./media/cache-configure/redis-cache-access-ports.png)

#### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Maxmemory-policy e maxmemory-reserved
As configurações **Maxmemory-policy** e **maxmemory-reserved** na folha **Configurações avançadas** definem as políticas de memória para o cache. A configuração **maxmemory-policy** define a política de remoção para o cache e **maxmemory-reserved** define a memória reservada para processos não cache.

![Política Maxmemory de Cache Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

A **política Maxmemory** permite que você escolha uma das políticas de remoção a seguir:

* `volatile-lru` – esse é o padrão.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para saber mais sobre políticas `maxmemory`, consulte [Políticas de remoção](http://redis.io/topics/lru-cache#eviction-policies).

A configuração **maxmemory-reserved** configura a quantidade de memória em MB reservada para operações de não cache, como replicação, durante o failover. Ela também pode ser usada quando você tem uma taxa alta de fragmentação. Definir esse valor permite que você tenha uma experiência mais consistente do servidor Redis quando sua carga varia. Esse valor deve ser definido como maior para cargas de trabalho que executam muitas operações de gravação. Quando a memória é reservada para essas operações, ela não fica disponível para o armazenamento de dados armazenados em cache.

> [!IMPORTANT]
> A configuração **maxmemory-reserved** está disponível apenas para os caches Standard e Premium.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações de Keyspace (configurações avançadas)
As notificações keyspace do Redis são configuradas na folha **Configurações avançadas** . As notificações keyspace permitem que clientes recebam notificações quando determinados eventos ocorrerem.

![Configurações avançadas de Cache Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> As notificações de Keyspace e a configuração **notify-keyspace-events** estão disponíveis apenas para os caches Standard e Premium.
> 
> 

Para obter mais informações, veja [Notificações de Keyspace do Redis](http://redis.io/topics/notifications). Para o código de exemplo, veja o arquivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) no exemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Supervisor do Cache Redis
A folha do **Supervisor do Cache Redis** exibe recomendações para seu cache. Durante as operações normais, nenhuma recomendação é exibida. 

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se qualquer condição ocorrer durante as operações de seu cache, por exemplo, alto uso de memória, de largura de banda de rede ou de carga do servidor, um alerta será exibido na folha **Cache Redis** .

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

É possível encontrar mais informações na folha **Recomendações** .

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Você pode monitorar essas métricas nas seções [Gráficos de monitoramento](cache-how-to-monitor.md#monitoring-charts) e [Gráficos de uso](cache-how-to-monitor.md#usage-charts) da folha **Cache Redis**.

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Se o cache se aproximar da capacidade máxima para essas métricas durante um período prolongado, uma recomendação será criada. Para saber mais sobre as métricas e os limites revisados pela ferramenta **Recomendações**, confira a tabela a seguir:

| Métrica do Cache Redis | Mais informações |
| --- | --- |
| Uso de largura de banda de rede |[Desempenho do cache - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes conectados |[Configuração padrão do servidor Redis - maxclients](#maxclients) |
| Carga do servidor |[Gráficos de uso - carga do servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso de memória |[Desempenho do cache - tamanho](cache-faq.md#cache-performance) |

Para atualizar o cache, clique em **Atualizar agora** a fim de alterar o [tipo de preço](#pricing-tier) e dimensionar seu cache. Para obter mais informações sobre como escolher um tipo de preço, confira [Qual oferta e tamanho de Cache Redis devo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="pricing-tier"></a>Tipo de preço
Clique em **Tipo de preço** para exibir ou alterar o tipo de preço do cache. Para obter mais informações sobre escala, veja [Como escalonar o Cache Redis do Azure](cache-how-to-scale.md).

![Tipo de preço do Cache Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do cluster Redis
Clique em **Tamanho do Cluster Redis (VISUALIZAÇÃO)** para alterar o tamanho do cluster de um cache premium em execução com clustering habilitado.

> [!NOTE]
> Observe que, enquanto a camada Cache Redis do Azure Premium foi liberada para disponibilidade geral, o recurso de Tamanho do Cluster Redis está atualmente em visualização.
> 
> 

![Tamanho do cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **Contagem de fragmentos** e clique em **OK** para salvar.

> [!IMPORTANT]
> O clustering está disponível apenas para os Caches premium. Para saber mais, consulte [Como configurar um cluster para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Persistência de dados do Redis
Clique em **Persistência de dados do Redis** para habilitar, desabilitar ou configurar a persistência de dados para o cache premium.

![Persistência de dados do Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Para habilitar a persistência do Redis, clique em **Habilitado** para habilitar o backup do RDB (banco de dados do Redis). Para desabilitar a persistência do Redis, clique em **Desabilitado**.

Para configurar o intervalo de backup, selecione uma das seguintes entradas de **Frequência de Backup** na lista suspensa. 

- **15 minutos**
- **30 minutos**
- **60 minutos**
- **Seis horas**
- **12 horas**
- **24 horas**

Esse intervalo de backup inicia a contagem regressiva depois que a operação de backup anterior for concluída com êxito e quando ela expira, um novo backup é iniciado.

Clique em **Conta de Armazenamento** para selecionar a conta de armazenamento a ser usada e escolha a **Chave primária** ou **Chave secundária** a ser usada na lista suspensa **Chave de Armazenamento**. Você deve escolher uma conta de armazenamento na mesma região que o cache e uma conta do **Armazenamento Premium** é recomendada, pois o armazenamento premium tem uma maior taxa de transferência. Sempre que a chave de armazenamento para a sua conta de persistência é regenerada, você deve escolher novamente a chave desejada no menu suspenso **Chave de Armazenamento**.

Clique em **OK** para salvar a configuração de persistência.

> [!IMPORTANT]
> A persistência de dados do Redis só está disponível para os caches Premium. Para obter mais informações, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).
> 
> 

### <a name="schedule-updates"></a>Agendar atualizações
A folha **Agendar atualizações** permite designar uma janela de manutenção para atualizações do servidor Redis do seu cache. 

> [!IMPORTANT]
> A janela de manutenção se aplica apenas às atualizações do servidor Redis e não a quaisquer atualizações do Azure ou atualizações do sistema operacional das VMs que hospedam o cache.
> 
> 

![Agendar atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, marque os dias desejados, especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Observe que o horário da janela de manutenção é em UTC. 

> [!IMPORTANT]
> A funcionalidade **Agendar atualizações** está disponível somente para caches do nível Premium. Para saber mais e instruções, confira [Como administrar o Cache Redis do Azure – Agendar atualizações](cache-administration.md#schedule-updates).
> 
> 



## <a name="virtual-network"></a>Rede Virtual
A seção **Rede Virtual** permite que você defina as configurações de rede virtual para o cache. Para saber mais sobre como criar um cache premium com suporte de rede virtual e atualizar suas configurações, confira [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> As configurações de rede virtual só estão disponíveis para os caches premium configurados com suporte para rede virtual durante a criação do cache. 
> 
> 


### <a name="properties"></a>Propriedades
Clique em **Propriedades** para exibir informações sobre o cache, incluindo o ponto de extremidade e as portas do cache.

![Propriedades de Cache Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
A seção **Bloqueios** permite bloquear uma assinatura, um recurso ou um grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script de automação

Clique em **Script de automação** para compilar e exportar um modelo de seus recursos implantados para implantações futuras. Para saber mais sobre como trabalhar com modelos, confira [Implantar recursos com modelos do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Configurações de administração
As configurações na seção **Administração** permitem que você execute as tarefas administrativas a seguir para seu cache premium. 

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar Dados](#importexport)
* [Reboot](#reboot)


> [!IMPORTANT]
> As configurações nesta seção só estão disponíveis para os caches da camada Premium.
> 
> 

### <a name="importexport"></a>Importar/exportar
A Importação/Exportação é uma operação de gerenciamento de dados do Cache Redis do Azure que permite importar e exportar dados para o cache importando e exportando um instantâneo do RDB (Banco de Dados do Cache Redis) de um cache premium para um blob de páginas em uma Conta de Armazenamento do Azure. A Importação/Exportação permite migrar entre diferentes instâncias do Cache Redis do Azure ou popular o cache com os dados antes de usar.

A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache Redis do Azure carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

A exportação permite exportar os dados armazenados no Cache Redis do Azure para arquivos RDB compatíveis com Redis. Você pode usar esse recurso para mover dados de uma instância do Cache Redis do Azure para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância de servidor do Cache Redis do Azure e o arquivo é carregado para a conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

> [!IMPORTANT]
> A opção Importar/Exportar está disponível somente para caches do nível Premium. Para saber mais e instruções, confira [Importar e exportar dados no Cache Redis do Azure](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reboot
A folha **Reinicializar** permite a reinicialização dos nós do cache. Essa funcionalidade de reinicialização permite que você teste seu aplicativo para garantir a resiliência caso ocorra uma falha de um nó de cache.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Se tiver um cache premium com clustering habilitado, você poderá selecionar quais fragmentos do cache serão reinicializados.

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reinicializar um ou mais nós do cache, selecione os nós desejados e clique em **Reinicializar**. Se tiver um cache premium com clustering habilitado, selecione os fragmentos a serem reinicializados e clique em **Reinicializar**. Depois de alguns minutos, os nós selecionados são reinicializados e voltam a ficar online alguns minutos mais tarde.

> [!IMPORTANT]
> A reinicialização está disponível somente para caches do nível Premium. Para saber mais e instruções, confira [Como administrar o Cache Redis do Azure – Reinicializar](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitoramento

A seção **Monitoramento** permite que você configure o diagnóstico e o monitoramento para seu Cache Redis. Para saber mais sobre o diagnóstico e monitoramento do Cache Redis do Azure, confira [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md).

![Diagnostics](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas do Redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnostics](#diagnostics)

### <a name="redis-metrics"></a>Métricas do Redis
Clique em **Métricas do Redis** para [exibir métricas](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) para seu cache.

### <a name="alert-rules"></a>Regras de alerta

Clique em **Regras de alerta** para configurar alertas com base nas métricas do Cache Redis. Para obter mais informações, consulte [Operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="diagnostics"></a>Diagnostics

Clique em **Diagnóstico** para [configurar a conta de armazenamento](cache-how-to-monitor.md#enable-cache-diagnostics) usada para armazenar o diagnóstico do cache.

![Diagnósticos de Cache Redis](./media/cache-configure/redis-cache-diagnostics-settings.png)

## <a name="support--troubleshooting-settings"></a>Configurações de suporte e solução de problemas
As configurações na seção **Suporte + solução de problemas** fornecem opções para resolver problemas com o cache.

![Suporte + solução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Integridade de recursos](#resource-health)
* [Nova solicitação de suporte](#new-support-request)

### <a name="resource-health"></a>Integridade de recursos
**Resource Health** observa seu recurso e informa se ele está sendo executado conforme o esperado. Para saber mais sobre o serviço Azure Resource Health, confira [Visão geral do Azure Resource Health](../resource-health/resource-health-overview.md).

> [!NOTE]
> A integridade de recursos atualmente não consegue relatar a integridade de instâncias de Cache Redis do Azure hospedadas em uma rede virtual. Para saber mais, confira [Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nova solicitação de suporte
Clique em **Nova solicitação de suporte** para abrir uma solicitação de suporte para seu cache.





## <a name="default-redis-server-configuration"></a>Configuração padrão do servidor Redis
Novas instâncias de Cache Redis do Azure são configuradas com os seguintes valores de configuração padrão Redis.

> [!NOTE]
> As configurações nesta seção não podem ser alteradas com o método `StackExchange.Redis.IServer.ConfigSet`. Se esse método for chamado com um dos comandos nesta seção, será gerada uma exceção similar à seguinte:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Todos os valores que podem ser configurados, como **max-memory-policy**, podem ser configurados por meio do Portal do Azure ou de ferramentas de gerenciamento de linha de comando, como a CLI do Azure ou o PowerShell.
> 
> 

| Configuração | Valor padrão | Descrição |
| --- | --- | --- |
| `databases` |16 |O número de bancos de dados padrão é 16, mas você pode configurar um número diferente com base no tipo de preço.<sup>1</sup> O banco de dados padrão é o DB 0; você poderá selecionar um diferente por conexão usando `connection.GetDatabase(dbid)`, em que `dbid` é um número entre `0` e `databases - 1`. |
| `maxclients` |Depende do tipo de preço<sup>2</sup> |Esse é o número máximo de clientes conectados permitidos ao mesmo tempo. Quando o limite é atingido o Redis fecha todas as novas conexões, retornando um erro de 'número máximo de clientes atingido'. |
| `maxmemory-policy` |`volatile-lru` |A política Maxmemory é a configuração de como o Redis seleciona o que remover quando `maxmemory` (o tamanho da oferta de cache que você selecionou quando criou o cache) é atingido. Com o Cache Redis do Azure, a configuração padrão é `volatile-lru`, que remove as chaves com um conjunto de expiração usando um algoritmo LRU. Essa configuração pode ser definida no portal do Azure. Para saber mais, confira [Maxmemory-policy e maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved). |
| `maxmemory-sample`s |3 |Para economizar memória, LRU e algoritmos TTL mínimos são algoritmos aproximados, em vez de algoritmos precisos. Por padrão, o Redis verificará três chaves e escolherá aquela que foi usada há mais tempo. |
| `lua-time-limit` |5.000 |Tempo máximo de execução de um script Lua em milissegundos. Se o tempo de execução máximo for atingido, o Redis registrará em log que um script ainda está em execução depois do tempo máximo permitido e começará a responder a consultas com um erro. |
| `lua-event-limit` |500 |O tamanho máximo da fila de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Os limites de buffer de saída do cliente podem ser usados para impor a desconexão de clientes que não estão lendo dados do servidor de forma rápida o suficiente, por algum motivo (uma razão comum é que um cliente Pub/Sub não consegue consumir mensagens de forma tão rápida quanto o editor consegue produzi-las). Para obter mais informações, veja [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a> 
<sup>1</sup>O limite para `databases` é diferente para cada tipo de preço do Cache Redis do Azure e pode ser definido na criação do cache. Se nenhuma configuração `databases` for especificada durante a criação do cache, o padrão será 16.

* Caches Básico e Standard
  * Cache C0 (250 MB) - até 16 bancos de dados
  * Cache C1 (1 GB) - até 16 bancos de dados
  * Cache C2 (2,5 GB) - até 16 bancos de dados
  * Cache C3 (6 GB) - até 16 bancos de dados
  * Cache C4 (13 GB) - até 32 bancos de dados
  * Cache C5 (26 GB) - até 48 bancos de dados
  * Cache C6 (53 GB) - até 64 bancos de dados
* Caches Premium
  * P1 (6 GB - 60 GB) - até 16 bancos de dados
  * P2 (13 - 130 GB) - até 32 bancos de dados
  * P3 (26 GB - 260 GB) - até 48 bancos de dados
  * P4 (53 - 530 GB) - até 64 bancos de dados
  * Todos os caches premium com cluster Redis habilitado – o cluster Redis permite apenas o uso do banco de dados 0 para que o limite `databases` de qualquer cache premium com o cluster Redis habilitado seja efetivamente 1 e o comando [Select](http://redis.io/commands/select) não seja permitido. Para saber mais, confira [Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

> [!NOTE]
> As configurações `databases` pode ser definida somente durante a criação do cache e apenas usando PowerShell, CLI ou outros clientes de gerenciamento. Para obter um exemplo de configuração `databases` durante a criação de cache usando o PowerShell, confira [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a> 
<sup>2</sup>`maxclients` é diferente para cada tipo de preço do Cache Redis do Azure.

* Caches Básico e Standard
  * Cache C0 (250 MB) - até 256 conexões
  * Cache C1 (1 GB) - até 1.000 conexões
  * Cache C2 (2.5 GB) - até 2.000 conexões
  * Cache C3 (6 GB) - até 5.000 conexões
  * Cache C4 (13 GB) - até 10.000 conexões
  * Cache C5 (26 GB) - até 15.000 conexões
  * Cache C6 (53 GB) - até 20.000 conexões
* Caches Premium
  * P1 (6 GB - 60 GB) - até 7.500 conexões
  * P2 (13 GB - 130 GB) - até 15.000 conexões
  * P3 (26 GB - 260 GB) - até 30.000 conexões
  * P4 (53 GB - 530 GB) - até 40.000 conexões

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Comandos Redis não têm suporte no Cache Redis do Azure
> [!IMPORTANT]
> Como a configuração e o gerenciamento de instâncias de Cache Redis do Azure é gerenciada pela Microsoft, os comandos a seguir são desabilitados. Se você tentar invocá-los, receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * Salvar
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER - Os comandos de gravação do cluster estão desabilitados, mas comandos do Cluster somente leitura são permitidos.
> 
> 

Para saber mais sobre os comandos do Redis, confira [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Console do Redis
Você pode emitir com segurança comandos para as suas instâncias do Cache Redis do Azure usando o **Console do Redis**, que está disponível para os caches Standard e Premium.

> [!IMPORTANT]
> O Console Redis não funciona com a Rede Virtual, o clustering e bancos de dados diferentes de 0. 
> 
> * [VNET](cache-how-to-premium-vnet.md) – quando o seu cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como o Console do Redis usa o cliente redis cli.exe hospedado em máquinas virtuais que não fazem parte da sua VNET, não pode se conectar ao seu cache.
> * [Clustering](cache-how-to-premium-clustering.md) – o Console do Redis usa o cliente redis-cli.exe que atualmente não dá suporte ao clustering. O utilitário redis-cli na ramificação [instável](http://redis.io/download) do repositório do Redis no GitHub implementa um suporte básico quando iniciado com a opção `-c`. Para obter mais informações, consulte [Brincando com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) em [http://redis.io](http://redis.io) no [Tutorial do cluster Redis](http://redis.io/topics/cluster-tutorial).
> * O Console Redis cria uma nova conexão com o banco de dados 0 toda vez que você enviar um comando. Não é possível usar o comando `SELECT` para selecionar um banco de dados diferente, pois o banco de dados é redefinido para 0 com cada comando. Para obter informações sobre como executar comandos do Redis, incluindo alteração para outro banco de dados, confira [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
> 
> 

Para acessar o Console do Redis, clique em **Console** na folha **Cache Redis**.

![Console do Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos em sua instância de cache, simplesmente digite no comando desejado no console.

![Console do Redis](./media/cache-configure/redis-console.png)

Para obter uma lista de comandos do Redis que estão desabilitados para o Cache Redis do Azure, veja a seção anterior [Comandos do Redis sem suporte no Cache Redis do Azure](#redis-commands-not-supported-in-azure-redis-cache) . Para saber mais sobre os comandos do Redis, confira [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Mover o cache para uma nova assinatura
Você pode mover o cache para uma nova assinatura clicando em **Mover**.

![Mover o Cache Redis](./media/cache-configure/redis-cache-move.png)

Para saber mais sobre como mover os recursos de um grupo de recursos para outro, e de uma assinatura para outra, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como trabalhar com os comandos do Redis, consulte [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)


