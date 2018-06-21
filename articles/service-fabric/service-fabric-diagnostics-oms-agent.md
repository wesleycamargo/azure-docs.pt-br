---
title: Azure Service Fabric – Monitoramento de Desempenho com o Log Analytics | Microsoft Docs
description: Saiba como configurar o Agente do OMS para monitorar os contêineres e os contadores de desempenho para os clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 74a738f85a969e3c3451dc326de9b4284c0984c8
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809566"
---
# <a name="performance-monitoring-with-log-analytics"></a>Monitoramento do Desempenho com Log Analytics

Este artigo aborda as etapas para adicionar o Log Analytics, também conhecido como Agente do OMS, como extensão do conjunto de dimensionamento de máquinas virtuais e conectá-lo ao espaço de trabalho do Log Analytics do Azure existente. Isso permite coletar dados de diagnóstico sobre contêineres, aplicativos e monitoramento do desempenho. Ao adicioná-lo como uma extensão para o recurso de conjunto de dimensionamento de máquinas virtuais, o Azure Resource Manager garante que ele seja instalado em cada nó, mesmo ao dimensionar o cluster.

> [!NOTE]
> Este artigo pressupõe que você já tenha o espaço de trabalho do Log Analytics do Azure configurado. Se você não tiver, consulte [Configurar o Log Analytics do Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicionar a extensão do agente por meio da CLI do Azure

A melhor maneira de adicionar o Agente do OMS ao cluster é por meio das APIs de conjunto de dimensionamento de máquinas virtuais disponíveis com a CLI do Azure. Se você não tiver a CLI do Azure configurada ainda, vá para o Portal do Azure e abra uma instância do [Cloud Shell](../cloud-shell/overview.md) ou [instale a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Depois que o Cloud Shell for solicitado, certifique-se de que você esteja trabalhando na mesma assinatura do recurso. Verifique isso com `az account show` e certifique-se de que o valor “nome” corresponde ao da assinatura de cluster.

2. No Portal, navegue até o grupo de recursos em que o espaço de trabalho do Log Analytics está localizado. Clique no recurso do Log Analytics (o tipo do recurso será o Log Analytics). Quando estiver na página de visão geral do recurso, clique em **Configurações Avançadas** na seção de Configurações no menu à esquerda.

    ![Página de propriedades do Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Clique em **Servidores Windows** se você estiver aguardando um cluster do Windows e **Servidores Linux** se estiver criando um cluster do Linux. Essa página mostrará a você o `workspace ID` e `workspace key` (listados como chave primária no portal). Você precisará deles para a próxima etapa.

4. Execute o comando para instalar o Agente do OMS no cluster, usando o API `vmss extension set` no Cloud Shell:

    Para um cluster do Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Para um cluster do Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Aqui está um exemplo de como o Agente do OMS sendo adicionado a um cluster do Windows.

    ![Comando de cli do Agente do OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Isso deve levar menos de 15 minutos para adicionar com êxito o agente aos nós. Você pode verificar se os agentes foram adicionados usando a API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicionar o agente por meio do modelo do Resource Manager

Os modelos do Gerenciador de Recursos de exemplo que implantam o espaço de trabalho do Log Analytics do Azure e adiciona um agente a cada um dos nós estão disponíveis para [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Você pode baixar e modificar este modelo para implantar um cluster que melhor atenda às suas necessidades.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Exibir Contadores de Desempenho no Portal do Log Analytics

Agora que você adicionou o agente do OMS, vá direto para o portal do Log Analytics para escolher quais contadores de desempenho você deseja coletar. 

1. No portal do Azure, vá para o grupo de recursos em que você criou a solução Análise do Service Fabric. Selecione **ServiceFabric\<nomeDoEspaçoDeTrabalhoOMS\>**.

2. Clique em **Espaço de Trabalho OMS**.

3. Clique em **Configurações Avançadas**.

4. Clique em **Dados**, depois clique em **Contadores de Desempenho Windows ou Linux**. Há uma lista de contadores padrão que você pode habilitar, além de também poder definir o intervalo de coleta. Você pode também adicionar [contadores de desempenho adicionais](service-fabric-diagnostics-event-generation-perf.md) para coletar. O formato correto é referenciado neste [artigo](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Clique em **Salvar**, depois em **OK**.

6. Feche a folha Configurações Avançadas.

7. No título Geral, clique em **Visão geral**.

8. Quando você estiver usando o portal, verá um bloco na forma de um grafo para cada uma das soluções habilitadas, inclusive uma para Service Fabric. Clique no grafo **Service Fabric** para ir para a solução de Análise do Service Fabric.

9. Você verá alguns blocos com elementos gráficos no canal operacional e eventos de serviços confiáveis. A representação gráfica dos dados que fluem para os contadores selecionados aparecerá em Métricas de Nó. 

10. Clique em um grafo de Métrica de Contêiner para ver detalhes adicionais. Você também pode consultar dados do contador de desempenho da mesma forma que os eventos de cluster e filtrar por nome do contador de desempenho, nós e valores, usando a linguagem de consulta Kusto.

![Consulta de contador de desempenho do OMS](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Próximas etapas

* Coletar [contador de desempenho](service-fabric-diagnostics-event-generation-perf.md) relevantes. Para configurar o agente do OMS para coletar contadores de desempenho específicos, veja [configurando fontes de dados](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Configure o Log Analytics para definir [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e no diagnóstico
* Como alternativa, você pode coletar os contadores de desempenho por meio da [extensão de Diagnóstico do Azure e enviá-los ao Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
