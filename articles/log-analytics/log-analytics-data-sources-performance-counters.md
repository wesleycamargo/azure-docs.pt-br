<properties 
   pageTitle="Contadores de desempenho do Windows e Linux no Log Analytics | Microsoft Azure"
   description="Os contadores de desempenho são coletados pelo Log Analytics para analisar o desempenho em agentes do Windows e Linux. Este artigo descreve como configurar a coleta de contadores de desempenho para agentes do Windows e do Linux, os detalhes deles que são armazenados no repositório do OMS e como analisá-los no portal do OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2016"
   ms.author="bwren" />

# Fontes de dados de desempenho do Windows e Linux no Log Analytics 

Os contadores de desempenho no Windows e Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operacionais e aplicativos. O Log Analytics pode coletar contadores de desempenho em intervalos frequentes para análises NRT (Quase em Tempo Real) além de agregar dados de desempenho para análise e relatório de longo prazo.

![Contadores de desempenho](media/log-analytics-data-sources-performance-counters/overview.png)

## Configurando os contadores de desempenho

Configure os contadores de desempenho do [menu Dados nas Configurações do Log Analytics](log-analytics-data-sources.md/configuring-data-sources).

Quando você configurar os contadores de desempenho do Linux ou Windows para um novo espaço de trabalho do OMS pela primeira vez, você terá a opção de criar rapidamente vários contadores comuns. Eles serão listados com uma caixa de seleção ao lado de cada um. Garanta que todos os contadores que deseja criar inicialmente estejam marcados e clique em **Add the selected performance counters** (Adicionar os contadores de desempenho selecionados).

![Configurar contadores de desempenho do Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para coletar.

1. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\\contador*. Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns. Você pode selecionar um contador na lista ou digitar um dos seus. Você também pode retornar todas as instâncias de um determinado contador especificando *objeto\\contador*. 
2. Clique em **+** ou pressione **Enter** para adicionar o contador à lista.
3. Quando você adicionar um contador, ele usará o padrão de 10 segundos para seu **Intervalo de Amostragem**. Você poderá alterar isso para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
4. Quando terminar de adicionar contadores, clique no botão **Salvar** na parte superior da tela para salvar a configuração.

![Configurar contadores de desempenho do Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho do Linux para coletar.

1. Por padrão, todas as alterações de configuração são automaticamente envidas por push para todos os agentes. Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd. Se você quiser modificar esse arquivo manualmente em cada agente do Linux, desmarque a caixa *Apply below configuration to my Linux machines* (Aplicar as configurações abaixo aos computadores Linux).
2. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\\contador*. Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns. Você pode selecionar um contador na lista ou digitar um dos seus.  
2. Clique em **+** ou pressione **Enter** para adicionar o contador à lista de outros contadores para o objeto.
3. Todos os contadores para um objeto usarão o mesmo **Intervalo de Amostragem**. O padrão é 10 segundos e você poderá alterar isso para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
4. Quando terminar de adicionar contadores, clique no botão **Salvar** na parte superior da tela para salvar a configuração.

## Coleta de dados

O Log Analytics coletará todos os contadores de desempenho especificados no seu intervalo de amostragem especificado em todos os agentes que têm o contador instalado. Os dados brutos estarão disponíveis por 14 dias na exibição de gráfico expandida no console do OMS.

Todos os dados de desempenho coletados são agregados em intervalos de 30 minutos. Os dados agregados estão disponíveis em todas as exibições de pesquisa de log pela duração especificada por sua assinatura do OMS.


## Propriedades do registro de desempenho

Os registros de desempenho são criados por meio dos dados de desempenho agregados durante os intervalos de 30 minutos. O valor do registro é o valor médio do contador ao longo dos 30 minutos anteriores. Não são criados registros para dados brutos de NRT. Os dados brutos estão disponíveis apenas na exibição **Métricas** do console do OMS.

Os registros de desempenho têm um tipo de **Perf** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Computador | Computador do qual o evento foi coletado. |
| CounterName | Nome do contador de desempenho |
| CounterPath | Caminho completo do contador no formato \\\<Computador>\\objeto(instância)\\contador. |
| CounterValue | Valor numérico do contador agregados em 30 minutos. |
| InstanceName | Nome da instância do evento. Vazio se não houver nenhuma instância. |
| ObjectName | Nome do objeto de desempenho |
| SourceSystem | Tipo de agente do qual os dados foram coletados. <br> OpsManager: agente do Windows, conexão direta ou SCOM <br> Linux: todos os agentes do Linux <br> AzureStorage: Diagnóstico do Azure |
| TimeGenerated | Data e hora em que os dados foram amostrados. |


## Estimativas de dimensionamento

 Uma estimativa aproximada de coleção de um determinado contador em intervalos de 10 segundos é cerca de 1 MB por dia por instância. Você pode estimar os requisitos de armazenamento de um contador específico com a fórmula a seguir.

	1 MB x (number of counters) x (number of agents) x (number of instances)

## Pesquisas de log com registros de desempenho

A tabela a seguir fornece diferentes exemplos de pesquisas de log que recuperam registros de desempenho.

| Consultar | Descrição |
|:--|:--|
| Type=Perf | Todos os dados de desempenho |
| Type=Perf Computer="MyComputer" | Todos os dados de desempenho de um computador específico |
| Type=Perf CounterName="Current Disk Queue Length" | Todos os dados de desempenho de um contador específico |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=\_Total measure Avg(Average) as AVGCPU by Computer | Utilização média da CPU em todos os computadores |
| Type=Perf (CounterName="% Processor Time") measure max(Max) by Computer | Utilização máxima da CPU em todos os computadores |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" measure Avg(Average) by InstanceName | Comprimento médio da fila de disco atual em todas as instâncias de um determinado computador |
| Type=Perf CounterName="DiskTransfers/sec" measure percentile95(Average) by Computer | 95º percentil de transferências de disco/s em todos os computadores |
| Type=Perf CounterName="% Processor Time" InstanceName="\_Total" measure avg(CounterValue) by Computer Interval 1HOUR | Média por hora do uso da CPU em todos os computadores |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=\_Total measure percentile70(CounterValue) by CounterName Interval 1HOUR | Percentil 70 por hora de cada contador de percentagem % para um computador específico |
| Type=Perf CounterName="% Processor Time" InstanceName="\_Total" (Computer="MyComputer") measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR | Uso da CPU médio por horam mínimo, máximo e percentil 75 para um computador específico |

## Exibindo dados de desempenho

Quando você executa uma pesquisa de log de dados de desempenho, a exibição **Log** é mostrada por padrão. Essa exibição inclui registros de desempenho agregados. Para exibir os dados em formato gráfico, clique em **Métricas**. Clique em **+** ao lado de contador específico que você deseja exibir.

![Exibição de métricas recolhida](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)


Se o intervalo de tempo que você selecionou for de seis horas ou menos, o gráfico exibirá dados NRT e será atualizado em intervalos de segundos. Os dados dinâmicos serão exibidos no lado direito do gráfico em azul claro. Se você tiver um intervalo maior do que seis horas, o gráfico usará dados de agregação.

![Exibição de métricas expandida com dados dinâmicos](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

## Próximas etapas

- Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
- Exporte os dados coletados para o [Power BI](log-analytics-powerbi.md) para análise e visualizações adicionais.

<!-----HONumber=AcomDC_0504_2016-->