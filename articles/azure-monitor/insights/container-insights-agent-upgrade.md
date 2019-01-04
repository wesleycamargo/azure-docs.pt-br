---
title: Como Azure Monitor do Azure para contêineres (Visualizar) agente | Microsoft Docs
description: Este artigo descreve como atualizar o agente de Log Analytics usado pelo Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184076"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Como atualizar o Azure Monitor para o agente de contêineres (visualização)
O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Quando uma nova versão do agente é lançada, o agente é atualizado automaticamente nos clusters do Kubernetes gerenciado hospedados no AKS (Serviço de Kubernetes do Azure).  

Caso a atualização do agente falhe, este artigo descreve o processo para atualizar manualmente o agente. Para seguir as versões lançadas, consulte [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Agente de upgrade no cluster do Kubernetes monitorado
O processo para atualizar o agente consiste em duas etapas diretas. A primeira etapa é desabilitar o monitoramento com o Azure Monitor para contêineres usando a CLI do Azure.  Siga as etapas descritas no artigo [Desativar monitoramento](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). O uso da CLI do Azure nos permite remover o agente dos nós no cluster sem afetar a solução e os dados correspondentes armazenados na área de trabalho. 

>[!NOTE]
>Enquanto você está executando esta atividade de manutenção, os nós no cluster não estão encaminhando dados coletados e as visualizações de desempenho não mostrarão dados entre o momento em que você remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas no artigo [Monitoramento Onboard](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) usando a CLI do Azure para concluir esse processo.  

Depois de reativar o monitoramento, pode levar cerca de 15 minutos até visualizar as métricas de integridade atualizadas do cluster. Para verificar se o agente foi atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

O status deve se parecer com o exemplo a seguir, em que os valores de *omi* e de *omsagent* devem corresponder à versão mais recente especificada no [histórico de versões do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>Próximas etapas
Se você tiver problemas ao atualizar o agente, revise o [guia de solução de problemas](container-insights-troubleshoot.md) para obter suporte.