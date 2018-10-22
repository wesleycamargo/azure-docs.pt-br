---
title: Monitorar o desempenho de cluster do AKS com o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode exibir e analisar dados de desempenho e de log com o Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: 6df7d42bc291713a815cac9f719f53136ed35b19
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956639"
---
## <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Compreender o desempenho de cluster do AKS com o Azure Monitor para contêineres
A exibição do desempenho de seus clusters do AKS (Serviço de Kubernetes do Azure) pode ser feita de duas perspectivas com o Azure Monitor para contêineres; exibindo diretamente de um cluster do AKS ou exibindo todos os clusters do AKS em uma assinatura do Azure Monitor. 

Este artigo ajudará você a entender a experiência com as duas perspectivas e como avaliar, investigar e resolver rapidamente os problemas detectados.

Para obter informações sobre como habilitar o Azure Monitor para contêineres, consulte [Integrar o Azure Monitor para contêineres](monitoring-container-insights-onboard.md).

O Azure Monitor oferece uma exibição de vários clusters, mostrando o status de integridade de todos os clusters do AKS implantados nos grupos de recursos de suas assinaturas, bem como os clusters do AKS não monitorados pela solução.  Imediatamente, você pode entender a integridade do cluster e, aqui, pode fazer drill down até a página de desempenho do nó e do controlador, ou pode navegar para ver os gráficos de desempenho do cluster.  Para clusters do AKS descobertos e identificados como não monitorados, é possível optar por habilitar o monitoramento para esse cluster a qualquer momento.  

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Entre no [Portal do Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Exibição de vários clusters do Azure Monitor 
Para exibir o status de integridade de todos os clusters do AKS implantados, selecione **Monitor** no painel esquerdo no portal do Azure.  Na seção **Percepções**, selecione **Contêineres (versão prévia)**.  

![Exemplo de painel com vários clusters no Azure Monitor](./media/monitoring-container-insights-analyze/azmon-containers-multiview.png)

Na guia **Clusters monitorados**, você pode obter as seguintes informações:

1. Quantos clusters estão com estado crítico ou não íntegro, e quantos estão íntegros ou não estão informando um estado (conhecidos como estado desconhecido)?
2. Quantos pods de sistemas, nós e usuários estão implantados por cluster.  

Os estados de integridade definidos são: 

1. **Íntegro** – nenhum problema detectado na VM, que está funcionando da maneira necessária. 
2. **Crítico** – foram detectados um ou mais problemas críticos, que precisam ser resolvidos para restaurar o estado operacional normal esperado.
3. **Aviso** – foram detectados um ou mais problemas, que precisam ser resolvidos para que a condição de integridade não se torne crítica.
4. **Desconhecido** – se o serviço não conseguir se conectar ao nó ou ao pod, o status mudará para um estado desconhecido.

O estado de integridade calcula o status geral do cluster como o "*o pior*" dos três estados, com uma exceção – se qualquer um dos três estados for *desconhecido*, o estado geral do cluster será **Desconhecido**.  

A tabela a seguir fornece um detalhamento do cálculo que controla os estados de integridade de um cluster monitorado na exibição de vários clusters.

| |Status |Disponibilidade |  
|-------|-------|-----------------|  
|**Pod de usuários**| | |  
| |Healthy |100% |  
| |Aviso |90 – 99% |  
| |Crítico |<90% |  
| |Desconhecido |Se não tiver sido relatado nos últimos 30 minutos |  
|**Pod de sistemas**| | |  
| |Healthy |100% |
| |Aviso |N/D |
| |Crítico |<100% |
| |Desconhecido |Se não tiver sido relatado nos últimos 30 minutos |
|**Node** | | |
| |Healthy |>85% |
| |Aviso |60 – 84% |
| |Crítico |<60% |
| |Desconhecido |Se não tiver sido relatado nos últimos 30 minutos |

Na lista de clusters, você pode fazer drill down até a página de **Cluster** clicando no nome do cluster, até a página de desempenho dos **Nós** clicando no rollup de nós na coluna **Nós** do cluster específico, ou pode fazer drill down até a página de desempenho de **Controladores** clicando no rollup da coluna **Pods de usuários** ou **Pods de sistemas**.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Exibir desempenho diretamente de um cluster do AKS
O acesso ao Azure Monitor para contêineres está disponível diretamente de um cluster do AKS selecionando **Insights (versão prévia)** do painel esquerdo. A exibição das informações sobre o cluster do AKS é organizada em quartos perspectivas:

- HDInsight
- Nós 
- Controladores  
- Contêineres

A página padrão aberta quando você clica em **Insights (versão prévia)** é **Cluster**, que inclui quatro gráficos de desempenho de linha que exibem as principais métricas de desempenho de seu cluster. 

![Gráficos de desempenho de exemplo na guia de Cluster](./media/monitoring-container-insights-analyze/containers-cluster-perfview.png)

O gráfico de desempenho exibe quatro métricas de desempenho:

- **Utilização da CPU do Nó&nbsp;%**: uma perspectiva agregada da utilização da CPU para todo o cluster. Você pode filtrar os resultados para o intervalo de tempo selecionando **Méd.**, **Mín.**, **Máx.**, **50º**, **90º** e **95º** no seletor de percentis acima do gráfico, sejam individualmente ou combinados. 
- **Utilização de memória do nó&nbsp;%**: uma perspectiva agregada da utilização de memória para todo o cluster. Você pode filtrar os resultados para o intervalo de tempo selecionando **Méd.**, **Mín.**, **Máx.**, **50º**, **90º** e **95º** no seletor de percentis acima do gráfico, sejam individualmente ou combinados. 
- **Contagem de nós**: uma contagem de nós e o status do Kubernetes. Os status de nós do cluster representado são *Todos*, *Pronto*, e *Não pronto*, e podem ser filtrados individualmente ou de modo combinado no seletor acima do gráfico. 
- **Contagem de pods de atividade**: contagem de pods e status do Kubernetes. Os status dos pods representados são *Todos*, *Pendente*, *Em execução* e *Desconhecido* e podem ser filtrados individualmente ou de modo combinado no seletor acima do gráfico. 

Quando você alterna para os **Nós**, **Controladores**, e guia **Contêineres**, exibido automaticamente no lado direito da página está o painel de propriedade.  Ele mostra as propriedades do item selecionado, incluindo rótulos que você definir para organizar objetos Kubernetes. Clique no **>>** link no painel de visualizar\ocultar o painel.  

![Exemplo do painel de propriedades de perspectivas de Kubernetes](./media/monitoring-container-insights-analyze/perspectives-preview-pane-01.png)

Como expandir os objetos na hierarquia, as atualizações de painel de propriedades com base no objeto selecionado. No painel também pode exibir eventos de Kubernetes com pesquisas de log predefinidas, clicando no link dos **logs de eventos de Kubernetes do modo de exibição** na parte superior do painel. Para obter informações adicionais sobre como exibir dados de log do Kubernetes, consulte [Pesquisar os logs para analisar dados](#search-logs-to-analyze-data).

Use a opção **+ Adicionar Filtro** na parte superior da página para filtrar os resultados na exibição segundo o **Serviço**, o **Nó** ou o **Namespace** e, após selecionar o escopo do filtro, você seleciona um dos valores mostrados no campo **Selecionar valor(es)**.  Após ser configurado, o filtro é aplicado globalmente ao exibir qualquer perspectiva do cluster do AKS.  A fórmula dá suporte apenas ao sinal de igual.  Você pode adicionar mais filtros depois do primeiro para restringir ainda mais os resultados.  Por exemplo, se você tiver especificado um filtro por **Nó**, o segundo filtro permitirá selecionar apenas **Serviço** ou **Namespace**.  

![Exemplo de uso do filtro para restringir os resultados](./media/monitoring-container-insights-analyze/add-filter-option-01.png)

Um filtro especificado em uma guia continua sendo aplicado quando você seleciona outro filtro e é excluído após você clicar no símbolo **x** ao lado dele.   

Alterne para a guia **Nós**, a hierarquia de linhas seguirá o modelo de objeto do Kubernetes começando com um nó no cluster. Expanda o nó e poderá exibir um ou mais pods em execução no nó. Se mais de um contêiner for agrupado em um pod, eles serão exibidos como a última linha na hierarquia. Você também poderá exibir quantas cargas de trabalho não relacionadas a pod estão em execução no host se o host tiver pressão de memória ou processador.

![Exemplo de hierarquia de nós do Kubernetes no modo de exibição de desempenho](./media/monitoring-container-insights-analyze/containers-nodes-view.png)

Você pode selecionar controladores ou contêineres na parte superior da página e examinar o status e a utilização de recursos para esses objetos.  Se, em vez disso, você quiser examinar a utilização de memória, na lista suspensa **Métrica**, selecione **RSS de Memória** ou **Conjunto de trabalho de memória**. **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e posteriores. Caso contrário, você exibirá valores para **Mín.&nbsp;%** como *NaN&nbsp;%*, que é um valor de tipo de dados numérico que representa um valor indefinido ou não representável. 

![Exibição do desempenho de nós do contêiner](./media/monitoring-container-insights-analyze/containers-node-metric-dropdown.png)

Por padrão, os dados de Desempenho são baseados nas últimas seis horas, mas você pode alterar a janela usando a opção **Intervalo de Tempo** na parte superior esquerda. Você também pode filtrar os resultados dentro do intervalo de tempo selecionando **Méd.**, **Mín.**, **Máx.**, **50º**, **90º** e **95º** no seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/monitoring-container-insights-analyze/containers-metric-percentile-filter.png)

No exemplo a seguir, observe que, para o nó *aks-nodepool1-*, o valor de **Contêineres** é 14, que é o valor acumulado do número total de contêineres implantados.

![Exemplo de acumulação de contêineres por nó](./media/monitoring-container-insights-analyze/containers-nodes-containerstotal.png)

Ele pode ajudá-lo a identificar rapidamente se você tem um balanço adequado de contêineres entre nós no seu cluster. 

As informações apresentadas quando você exibe Nós são descritas na tabela a seguir:

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do host. |
| Status | Exibição de Kubernetes do status do nó. |
| Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Percentual médio de nós com base no percentil pela duração selecionada. |
| Méd., Mín., Máx., 50º, 90º | Valor real de nós médio com base no percentil durante a duração de tempo selecionada. O valor médio é medido usando o limite de memória/CPU definido para um nó; para contêineres e pods, é o valor médio relatado pelo host. |
| Contêineres | Número de contêineres. |
| Tempo de atividade | Representa a hora desde que um nó foi iniciado ou reiniciado. |
| Controladores | Somente para os contêineres e pods. Mostra em que controlador ele está residindo. Nem todos os pods estão em um controlador, assim, alguns poderão exibir **N/D**. | 
| Tendência Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Tendência de gráfico de barras apresentando o percentual de métrica de percentil do controlador. |

No seletor, selecione **Controladores**.

![Selecionar modo de exibição de controladores](./media/monitoring-container-insights-analyze/containers-controllers-tab.png)

Aqui, você pode exibir a integridade do desempenho de seus controladores.

![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-insights-analyze/containers-controllers-view.png)

A hierarquia de linha começa com um controlador e expande o controlador. Você exibir um ou mais contêineres. Expanda um pod e a última linha mostrará o contêiner agrupado para o pod.  

As informações que são exibidas quando você exibe controladores são descritas na tabela a seguir:

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador.|
| Status | O status de acumulação dos contêineres quando a execução é concluída com status, como *OK*, *Encerrado*, *Com falha* *Parado* ou *Em pausa*. Se o contêiner estiver em execução, mas o status não tiver sido devidamente exibido ou não tiver sido selecionado pelo agente e não tiver respondido por mais de 30 minutos, o status será *Desconhecido*. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Média de acumulação do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Méd., Mín., Máx., 50º, 90º  | Acumulação da média de milinúcleo de CPU ou desempenho da memória do contêiner para o percentil selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador ou pod. |
| Reinícios | Acumulação da contagem de reinicialização dos contêineres. |
| Tempo de atividade | Representa o tempo desde o início de um contêiner. |
| Nó | Somente para os contêineres e pods. Mostra quais são os controladores residentes. | 
| Tendência Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;%| Tendência de gráfico de barras representando % de métrica percentil do controlador. |

Os ícones no campo status indicam o status online dos contêineres:
 
| ícone | Status | 
|--------|-------------|
| ![Ícone de status de execução pronto](./media/monitoring-container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou em pausa](./media/monitoring-container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/monitoring-container-insights-analyze/containers-grey-icon.png) | Última execução relatada, mas sem responder por mais de 30 minutos|
| ![Ícone de status de êxito](./media/monitoring-container-insights-analyze/containers-green-icon.png) | Parou com sucesso ou houve falha ao parar|

O ícone de status mostra uma contagem com base no que o pod fornece. Ele mostra os dois piores estados e, quando você focaliza o status, mostra um status acumulado de todos os pods no contêiner. Se não houver um estado pronto, o valor de status mostrará **(0)**. 

No seletor, selecione **Contêineres**.

![Selecionar modo de exibição de contêineres](./media/monitoring-container-insights-analyze/containers-containers-tab.png)

Aqui, você pode exibir a integridade de desempenho de seus contêineres do Kubernetes do Azure.  

![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-insights-analyze/containers-containers-view.png)

As informações que são exibidas quando você exibe contêineres são descritas na tabela a seguir:

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador.|
| Status | Status dos contêineres, se houver. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | A acumulação do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Méd., Mín., Máx., 50º, 90º  | A acumulação média do desempenho de memória ou do milinúcleo da CPU do contêiner para o percentual selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Pod | Contêiner no qual reside o pod.| 
| Nó |  Nó em que reside o contêiner. | 
| Reinícios | Representa o tempo desde o início de um contêiner. |
| Tempo de atividade | Representa a hora desde que um contêiner foi iniciado ou reiniciado. |
| Tendência Méd.&nbsp;%, Mín.&nbsp;%, Máx.&nbsp;%, 50º&nbsp;%, 90º&nbsp;% | Uma tendência de gráfico de barras que representa o percentual métrico médio do contêiner. |

Os ícones no campo status indicam os status online de pods, conforme descrito na tabela a seguir:
 
| ícone | Status |  
|--------|-------------|  
| ![Ícone de status de execução pronto](./media/monitoring-container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|  
| ![Ícone de status de espera ou em pausa](./media/monitoring-container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|  
| ![Ícone do status de execução informado pela última vez](./media/monitoring-container-insights-analyze/containers-grey-icon.png) | Relatado pela última vez como em execução, mas sem responder por mais de 30 minutos|  
| ![Ícone de status encerrado](./media/monitoring-container-insights-analyze/containers-terminated-icon.png) | Parou com sucesso ou houve falha ao parar|  
| ![Ícone de status com falha](./media/monitoring-container-insights-analyze/containers-failed-icon.png) | Estado com falha |  

## <a name="container-data-collection-details"></a>Detalhes da coleta de dados dos contêineres
O Container Insights coleta vários dados de log e métricas de desempenho de contêineres e hosts de contêiner. Os dados são coletados a cada três minutos.

### <a name="container-records"></a>Registros de contêiner

Exemplos de registros coletados pelo Azure Monitor para contêineres e os tipos de dados que aparecem nos resultados da pesquisa de logs são exibidos na tabela a seguir:

| Tipo de dados | Tipo de dados na Pesquisa de Log | Campos |
| --- | --- | --- |
| Desempenho de hosts e contêineres | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contêiner | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventário de imagem de contêiner | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log do contêiner | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log do serviço de contêiner | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventário de nós do contêiner | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Processo do contêiner | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventário de pods em um cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário da parte de nós de um cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Serviços no cluster Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós do cluster Kubernetes | Perf &#124; em que ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para a parte de contêineres do cluster Kubernetes | Perf &#124; em que ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Pesquisar logs para analisar dados
O Log Analytics pode ajudá-lo a procurar por tendências, diagnosticar afunilamentos, prever ou correlacionar dados que podem ajudar a determinar se a configuração do cluster atual está sendo executada corretamente. Pesquisas de logs predefinidas são fornecidas para você começar a usar imediatamente ou para personalizar para retornar as informações da maneira que você deseja. 

Você pode executar análises interativas dos dados no workspace selecionando a opção **Exibir logs de evento Kubernetes** ou **Visualizar logs de** contêiner no painel de visualização. A página **Pesquisa de Logs** aparece acima da página de portal do Azure em que você estava.

![Análise de dados no Log Analytics](./media/monitoring-container-insights-analyze/container-health-log-search-example.png)   

As saídas dos logs do contêiner encaminhada para o Log Analytics são STDOUT e STDERR. Uma vez que o Azure Monitor está monitorando o Kubernetes gerenciado pelo Azure (AKS), o Kube-system não é coletado hoje devido ao grande volume de dados gerado. 

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log
Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-las de acordo com seus requisitos. Para ajudar a criar consultas mais avançadas, você pode experimentar as seguintes consultas de amostra:

| Consultar | DESCRIÇÃO | 
|-------|-------------|
| ContainerInventory<br> &#124; projetar Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;processar tabela | Listar todas as informações do ciclo de vida do contêiner| 
| KubeEvents_CL<br> &#124; em que not(isempty(Namespace_s))<br> &#124; classificar por TimeGenerated desc<br> &#124;processar tabela | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; resumir AggregatedValue = count() by Image, ImageTag, Running | Inventário de imagem | 
| **Em Análise Avançada, selecione gráficos de linhas**:<br> Perf<br> &#124; em que ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; resumir AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU do contêiner | 
| **Em Análise Avançada, selecione gráficos de linhas**:<br> Perf &#124; em que ObjectName == "Container" e CounterName == "Memory Usage MB"<br> &#124; resumir AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memória de contêiner |
