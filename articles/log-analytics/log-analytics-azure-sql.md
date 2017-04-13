---
title: "Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs"
description: "A solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0184e95ca56e4bc4ffbe860da2b7a5cae9b5a043
ms.lasthandoff: 03/11/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorar o Banco de Dados do Azure SQL usando a Análise do Azure SQL (Visualização) no Log Analytics

A solução de Monitoramento do SQL Azure no Log Analytics do Azure coleta e visualiza métricas de desempenho importantes do SQL Azure. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. E você pode monitorar o Banco de Dados SQL e métricas de pool elástico em várias assinaturas do Azure e pools elásticos e visualizá-los. A solução também ajuda a identificar problemas em cada camada da pilha do aplicativo.  Ele usa [métricas de Diagnóstico do Azure](log-analytics-azure-storage.md) junto com modos de exibição de Log Analytics para apresentar dados sobre todos os bancos de dados do Azure SQL e pools elásticos em um único espaço de trabalho de Log Analytics.

Atualmente, essa solução de visualização dá suporte a até 5.000 Bancos de Dados do Azure SQL e 150.000 Pools Elásticos por espaço de trabalho.

A solução de Monitoramento do Azure SQL, como outras disponíveis para o Log Analytics, ajuda a monitorar e receber notificações sobre a integridade dos recursos do Azure (neste caso, o Banco de Dados SQL). O Banco de Dados SQL do Microsoft Azure é um serviço de banco de dados relacional escalonável que fornece recursos semelhantes aos recursos familiares do SQL Server para aplicativos em execução na nuvem do Azure. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

## <a name="connected-sources"></a>Fontes conectadas

A solução de Monitoramento do SQL do Azure não usa agentes para se conectar ao serviço de Log Analytics.

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suporte | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | Agentes do Linux diretos não são usados pela solução. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não | Uma conexão direta do agente do SCOM ao Log Analytics não é usada pela solução. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | O Log Analytics não lê os dados pré-existentes de uma conta de armazenamento. |
| [Diagnóstico do Azure](log-analytics-azure-storage.md) | Sim | Os dados de métricas do Azure são enviados para o Log Analytics diretamente pelo Azure. |

## <a name="prerequisites"></a>Pré-requisitos

1. Uma assinatura do Azure. Se não tiver uma, você poderá criá-la [grátis](https://azure.microsoft.com/free/).
2. Um espaço de trabalho do Log Analytics. Você pode usar um existente ou pode [criar um novo](log-analytics-get-started.md) para começar a usar essa solução.
3. Habilite o Diagnóstico do Azure para os bancos de dados do Azure SQL e pools elásticos e [configure-os para enviar os dados para o Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configuração

Execute as etapas a seguir para adicionar a solução de Monitoramento do Azure SQL a seu espaço de trabalho.

1. Adicione a solução de análise do SQL do Azure do [marketplace do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).
2. No portal do Azure, clique em **Novo** (o símbolo +) e, na lista de recursos, selecione **Monitoramento + Gerenciamento**.  
    ![Monitoramento + Gerenciamento](./media/log-analytics-azure-sql/monitoring-management.png)
3. Na lista **Monitoramento + Gerenciamento**, clique em **Ver todos**.
4. Na lista **Recomendado**, clique em **Mais** e, na nova lista, localize **Análise do Azure SQL (Visualização)** e selecione-a.  
    ![Solução de Análise do Azure SQL](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. Na folha **Análise do Azure SQL (Visualização)**, clique em **Criar**.  
    ![Criar](./media/log-analytics-azure-sql/portal-create.png)
6. Na folha **Criar nova solução**, selecione o espaço de trabalho ao qual você deseja adicionar a solução e clique em **Criar**.  
    ![adicionar ao espaço de trabalho](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Para configurar várias assinaturas do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Forneça a ID de recurso do espaço de trabalho como um parâmetro ao executar o script para enviar dados de diagnóstico de recursos em uma assinatura do Azure a um espaço de trabalho em outra assinatura do Azure.

**Exemplo**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução ao espaço de trabalho, o bloco de Análise do Azure SQL é adicionado ao espaço de trabalho e aparece na Visão geral. O bloco mostra o número de bancos de dados do Azure SQL e pools elásticos do Azure SQL aos quais a solução está conectada.

![Bloco de Análise do SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-monitoring-data"></a>Exibindo dados de Monitoramento do Azure SQL

Clique no bloco **Monitoramento do Azure SQL** para abrir o painel de Análise do Azure SQL. O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais itens que correspondem aos critérios da coluna para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de log que retorna todos os registros clicando em **Ver todos** na parte inferior da coluna ou clicando no cabeçalho de coluna.

Leia sobre [opções de Banco de Dados SQL e desempenho para as camadas de serviço](../sql-database/sql-database-service-tiers.md).



![Painel de Análise do SQL Azure](./media/log-analytics-azure-sql/azure-sql-dash-01.png)



![Painel de Análise do SQL Azure](./media/log-analytics-azure-sql/azure-sql-dash-02.png)

| Coluna | Descrição |
| --- | --- |
| **Análise de Banco de Dados do Azure SQL** | &nbsp; |
| N principais bancos de dados por utilização de DTU &gt; 90% | Este painel mostra o número de bancos de dados do Azure SQL que tiveram utilização de DTU de mais de 90% para o tempo selecionado. O lado superior mostra o número de bancos de dados que consumiram mais de 90% de disponibilidade de DTU total alocada durante o tempo especificado, de todos os bancos de dados que você está monitorando no Log Analytics.  Clique em um nome de banco de dados para executar uma pesquisa de log que mostra um gráfico de linha comparando a utilização de DTU do banco de dados em relação a todos os outros monitorados pelo espaço de trabalho. |
| N principais bancos de dados por utilização de CPU &gt; 90% | Este painel mostra o número de bancos de dados do Azure SQL que tiveram utilização da CPU de mais de 90% para o tempo selecionado. O lado superior mostra o número de bancos de dados que consumiram mais de 90% de disponibilidade de CPU total alocada durante o tempo especificado, de todos os bancos de dados que você está monitorando no Log Analytics.  Clique em um nome de banco de dados para executar uma pesquisa de log que mostra um gráfico de linha comparando a utilização de CPU do banco de dados em relação a todos os outros monitorados pelo espaço de trabalho. |
| N principais bancos de dados por consumo de armazenamento &gt; 90% | Este painel mostra o número de bancos de dados Azure SQL que consumiram mais de 90% da alocação de armazenamento para o tempo selecionado. O lado superior mostra o número de bancos de dados que violaram o limite de 90% durante o tempo especificado, de todos os bancos de dados que você está monitorando no Log Analytics.  Clique em um nome de banco de dados para executar uma pesquisa de log que mostra um gráfico de linha comparando o consumo de armazenamento do banco de dados em relação a todos os outros monitorados pelo espaço de trabalho. |
| **Pools Elásticos do SQL Azure** | &nbsp; |
| N principais pools elásticos por DTU &gt; 90% | Este painel mostra o número de pools elásticos do Azure SQL que consumiram mais de 90% da alocação total de DTU para o tempo selecionado. O lado superior mostra o número de pools elásticos que violaram o limite de 90% durante o mesmo horário especificado de todos os pools elásticos do SQL Azure que você está monitorando no Log Analytics.  Clique em um nome de pool elástico para executar uma pesquisa de log que mostra um gráfico de linha comparando o consumo de armazenamento do pool elástico em relação a todos os outros monitorados pelo espaço de trabalho. |
| N principais pools elásticos por CPU &gt; 90% | Este painel mostra o número de pools elásticos do Azure SQL que tiveram utilização da CPU de mais de 90% para o período de tempo selecionado. O lado superior mostra o número de pools elásticos que violaram o limite de 90% durante o mesmo horário especificado de todos os pools elásticos do SQL Azure que você está monitorando no Log Analytics.  Clique em um nome de pool elástico para executar uma pesquisa de log que mostra um gráfico de linha comparando a utilização de CPU do pool elástico em relação a todos os outros monitorados pelo espaço de trabalho. |
| N principais pools elásticos por consumo de armazenamento &gt; 90% | Este painel mostra o número de pools elásticos do Azure SQL que consumiram mais de 90% da alocação de armazenamento para o tempo selecionado. O lado superior mostra o número de pools elásticos que violaram o limite de 90% durante o mesmo horário especificado de todos os pools elásticos que você está monitorando no Log Analytics.  Clique em um nome de pool elástico para executar uma pesquisa de log que mostra um gráfico de linha comparando o consumo de armazenamento do pool elástico em relação a todos os outros monitorados pelo espaço de trabalho. |
| **Logs de Atividade do Azure SQL** | &nbsp; |
| Auditoria de Atividade do SQL Azure | Este painel mostra o número de registros de atividade do Azure relacionados ao Azure SQL para o tempo selecionado. Clique em um item para executar uma pesquisa de log que mostra detalhes adicionais sobre o item. |



### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A solução inclui consultas úteis para ajudar a analisar os dados. Se você rolar para a direita, o painel listará várias consultas comuns nas quais você poderá clicar para realizar uma [pesquisa de log](log-analytics-log-searches.md) e obter dados do Azure SQL.

![consultas](./media/log-analytics-azure-sql/azure-sql-queries.png)

A solução inclui algumas *consultas baseadas em alerta*, conforme mostrado acima, que você pode usar para alertar sobre limites específicos de bancos de dados do Azure SQL e pools elásticos.

#### <a name="to-configure-an-alert-for-your-workspace"></a>Para configurar um alerta para seu espaço de trabalho

1. Acesse o [portal do OMS](http://mms.microsoft.com/) e entre.
2. Abra o espaço de trabalho que você configurou para a solução.
3. Na página Visão geral, clique no bloco **Análise do Azure SQL (Visualização)**.
4. Role para a direita e clique em uma consulta para começar a criar um alerta.  
![consulta de alerta](./media/log-analytics-azure-sql/alert-query.png)
5. Na Pesquisa de Log, clique em **Alerta**.  
![criar alerta na pesquisa](./media/log-analytics-azure-sql/create-alert01.png)
6. Na página **Adicionar Regra de Alerta**, defina as propriedades adequadas e os limites específicos que você deseja e clique em **Salvar**.  
![adicionar regra de alerta](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-monitoring-data"></a>Agir sobre dados de Monitoramento do Azure SQL

Por exemplo, uma das consultas mais úteis que você pode executar é comparar a utilização de DTU para todos os Pools Elásticos do Azure SQL em todas as suas assinaturas do Azure. A DTU (Unidade de Taxa de Transferência de Banco de Dados) fornece uma maneira de descrever a capacidade relativa de um nível de desempenho de pools e bancos de dados Basic, Standard e Premium. DTUs são baseadas em uma medida combinada de CPU, memória, leituras e gravações. À medida que as DTUs aumentam, aumenta a capacidade oferecida pelo nível de desempenho. Por exemplo, um nível de desempenho com cinco DTUs tem cinco vezes mais energia do que um nível de desempenho com uma DTU. Uma cota de DTU máxima aplica-se a cada servidor e pool elástico.

Executando a consulta de Pesquisa de Log a seguir, você pode saber facilmente se está subutilizando ou sobreutilizando os pools elásticos do SQL Azure.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

No exemplo a seguir, você pode ver que um pool elástico tem alto uso de quase 100% de DTU, enquanto outros têm pouca utilização. Você pode investigar mais para solucionar possíveis alterações recentes no ambiente usando os logs de atividade do Azure.

![Resultados de pesquisa de log - alta utilização](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>Consulte também

- Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](log-analytics-alerts.md) quando ocorrerem eventos específicos do Azure SQL.

