---
title: "Azure Service Fabric – Configurando o monitoramento com o Agente do OMS | Microsoft Docs"
description: "Saiba como configurar o Agente do OMS para monitorar os contêineres e os contadores de desempenho para os clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Adicionar o Agente OMS a um cluster

Este artigo aborda as etapas para adicionar o Agente do OMS como extensão do conjunto de dimensionamento de máquinas virtuais e conectá-lo ao espaço de trabalho do Log Analytics do OMS existente. Isso permite coletar dados de diagnóstico sobre contêineres, aplicativos e monitoramento do desempenho. Ao adicioná-lo como uma extensão, o Azure Resource Manager garante que ele seja instalado em cada nó, mesmo ao dimensionar o cluster.

> [!NOTE]
> Este artigo pressupõe que você já tenha o espaço de trabalho do Log Analytics do OMS configurado. Se você não tiver, consulte [Configurar o Log Analytics do OMS](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicionar a extensão do agente por meio da CLI do Azure

A melhor maneira de adicionar o Agente do OMS ao cluster é por meio das APIs de conjunto de dimensionamento de máquinas virtuais disponíveis com a CLI do Azure. Se você não tiver a CLI do Azure configurada ainda, vá para o Portal do Azure e abra uma instância do [Cloud Shell](../cloud-shell/overview.md) ou [instale a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Depois que o Cloud Shell for solicitado, certifique-se de que você esteja trabalhando na mesma assinatura do recurso. Verifique isso com `az account show` e certifique-se de que o valor “nome” corresponde ao da assinatura de cluster.

2. No Portal, navegue até o grupo de recursos em que o espaço de trabalho do OMS está localizado. Clique no recurso Log Analytics (o tipo do recurso será Log Analytics), no painel de navegação à direita, role para baixo e clique em **Propriedades**.

    ![Página de propriedades do OMS](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Anote seu `workspaceId`. 

3. Você também precisará de seu `workspaceKey` para implantar o agente. Para obter a chave, clique em **Configurações Avançadas**, na seção *Configurações* do painel de navegação esquerdo. Clique em **Servidores Windows** se você estiver aguardando um cluster do Windows e **Servidores Linux** se estiver criando um cluster do Linux. Você precisará da *Chave Primária* que aparece para implantar os agentes, como o `workspaceKey`.

4. Execute o comando para instalar o Agente do OMS no cluster, usando o API `vmss extension set` no Cloud Shell:

    Para um cluster do Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Para um cluster do Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Aqui está um exemplo de como o Agente do OMS sendo adicionado a um cluster do Windows.

    ![Comando de cli do Agente do OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    Isso deve levar menos de 15 minutos para adicionar com êxito o agente aos nós. Você pode verificar se os agentes foram adicionados usando a API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicionar o agente por meio do modelo do Resource Manager

Os modelos do Resource Manager de exemplo que implantam o espaço de trabalho do Log Analytics do OMS e adiciona um agente a cada um dos nós estão disponíveis para [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Você pode baixar e modificar este modelo para implantar um cluster que melhor atenda às suas necessidades.

## <a name="next-steps"></a>Próximas etapas

* Coletar [contador de desempenho](service-fabric-diagnostics-event-generation-perf.md) relevantes. Para configurar o Agente do OMS para selecionar contadores de desempenho específicos, acesse o Portal do OMS (vinculado na parte superior do recurso do Log Analytics do OMS). Em seguida, clique em **Início > Configurações > Dados > Contadores de Desempenho do Windows** ou **Contadores de Desempenho de Linux** e escolha os contadores que você deseja coletar.
* Configure o OMS para configurar [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e diagnóstico
