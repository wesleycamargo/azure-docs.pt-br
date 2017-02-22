---
title: Coletar e analisar contadores de desempenho no Log Analytics | Microsoft Docs
description: "Os contadores de desempenho são coletados pelo Log Analytics para analisar o desempenho em agentes do Windows e Linux.  Este artigo descreve como configurar a coleta de contadores de desempenho para agentes do Windows e do Linux, os detalhes deles que são armazenados no repositório do OMS e como analisá-los no portal do OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: 1e4b5dac9333a9bd38f6ef89ddce22c74fed06ba


---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Fontes de dados de desempenho do Windows e Linux no Log Analytics
Os contadores de desempenho no Windows e Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operacionais e aplicativos.  O Log Analytics pode coletar contadores de desempenho em intervalos frequentes para análises NRT (Quase em Tempo Real) além de agregar dados de desempenho para análise e relatório de longo prazo.

![Contadores de desempenho](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurando os contadores de desempenho
Configure os contadores de desempenho do [menu Dados nas Configurações do Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Quando você configurar os contadores de desempenho do Linux ou Windows para um novo espaço de trabalho do OMS pela primeira vez, você tem a opção de criar rapidamente vários contadores comuns.  Eles são listados com uma caixa de seleção ao lado de cada um.  Garanta que todos os contadores que deseja criar inicialmente estejam marcados e clique em **Add the selected performance counters**(Adicionar os contadores de desempenho selecionados).

![Configurar contadores de desempenho do Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para coletar.

1. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\contador*.  Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns.  Você pode selecionar um contador na lista ou digitar um dos seus.  Você também pode retornar todas as instâncias de um determinado contador especificando *objeto\contador*. 
2. Clique em **+** ou pressione **Enter** para adicionar o contador à lista.
3. Quando você adicionar um contador, ele usa o padrão de 10 segundos para seu **Intervalo de Amostragem**.  Você poderá alterar isso para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
4. Quando terminar de adicionar contadores, clique no botão **Salvar** na parte superior da tela para salvar a configuração.

![Configurar contadores de desempenho do Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho do Linux para coletar.

1. Por padrão, todas as alterações de configuração são automaticamente envidas por push para todos os agentes.  Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd.  Se você quiser modificar esse arquivo manualmente em cada agente do Linux, desmarque a caixa *Apply below configuration to my Linux machines*(Aplicar as configurações abaixo aos computadores Linux).
2. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\contador*.  Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns.  Você pode selecionar um contador na lista ou digitar um dos seus.  
3. Clique em **+** ou pressione **Enter** para adicionar o contador à lista de outros contadores para o objeto.
4. Todos os contadores para um objeto usam o mesmo **Intervalo de Amostragem**.  O padrão é 10 segundos.  Você altera para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
5. Quando terminar de adicionar contadores, clique no botão **Salvar** na parte superior da tela para salvar a configuração.

## <a name="data-collection"></a>Coleta de dados
O Log Analytics coleta todos os contadores de desempenho especificados no seu intervalo de amostragem especificado em todos os agentes que têm o contador instalado.  Os dados não estão agregados e os dados brutos estão disponíveis em todas as exibições de pesquisa de log pela duração especificada por sua assinatura do OMS.

## <a name="performance-record-properties"></a>Propriedades do registro de desempenho
Os registros de desempenho têm um tipo de **Perf** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador do qual o evento foi coletado. |
| CounterName |Nome do contador de desempenho |
| CounterPath |Caminho completo do contador no formato \\\\\<Computador>\\objeto(instância)\\contador. |
| CounterValue |Valor numérico do contador. |
| InstanceName |Nome da instância do evento.  Vazio se não houver nenhuma instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente do qual os dados foram coletados. <br> OpsManager - agente do Windows, conexão direta ou SCOM <br> Linux: todos os agentes do Linux  <br> AzureStorage: Diagnóstico do Azure |
| TimeGenerated |Data e hora em que os dados foram amostrados. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada de coleção de um determinado contador em intervalos de 10 segundos é cerca de 1 MB por dia por instância.  Você pode estimar os requisitos de armazenamento de um contador específico com a fórmula a seguir.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Pesquisas de log com registros de desempenho
A tabela a seguir fornece diferentes exemplos de pesquisas de log que recuperam registros de desempenho.

| Consultar | Descrição |
|:--- |:--- |
| Type=Perf |Todos os dados de desempenho |
| Type=Perf Computer="MyComputer" |Todos os dados de desempenho de um computador específico |
| Type=Perf CounterName="Current Disk Queue Length" |Todos os dados de desempenho de um contador específico |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU by Computer |Utilização média da CPU em todos os computadores |
| Type=Perf (CounterName="% Processor Time") &#124; measure max(Max) by Computer |Utilização máxima da CPU em todos os computadores |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" &#124; measure Avg(Average) by InstanceName |Comprimento médio da fila de disco atual em todas as instâncias de um determinado computador |
| Type=Perf CounterName="DiskTransfers/sec" &#124; measure percentile95(Average) by Computer |95º percentil de transferências de disco/s em todos os computadores |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR |Por hora média de utilização da CPU em todos os computadores |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR |Percentil de 70 por hora de cada contador de porcentagem % para um computador específico |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR |Por hora média, mínima, máximo e percentil de 75 da CPU para um computador específico |

## <a name="viewing-performance-data"></a>Exibindo dados de desempenho
Quando você executa uma pesquisa de log de dados de desempenho, a exibição **Log** é mostrada por padrão.  Para exibir os dados em formato gráfico, clique em **Métricas**.  Para obter uma exibição gráfica detalhada, clique em **+** ao lado de um contador.  

![Exibição de métricas recolhida](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Se o intervalo de tempo que você selecionou for de seis horas ou menos, o gráfico é atualizado em intervalos de segundos.  Os dados dinâmicos são exibidos no lado direito do gráfico em azul claro.

![Exibição de métricas expandida com dados dinâmicos](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Para agregar dados de desempenho em uma pesquisa de log, confira [Agregação métrica sob demanda e visualização de OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
* Exporte os dados coletados para o [Power BI](log-analytics-powerbi.md) para análise e visualizações adicionais.




<!--HONumber=Jan17_HO4-->


