---
title: Como solucionar problemas do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode solucionar e resolver problemas com o Azure Monitor para contêineres.
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
ms.date: 10/19/2018
ms.author: magoedte
ms.openlocfilehash: 19d015947e5d2331c50cd64956e8a7650f96616d
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638317"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Solução de problemas do Azure Monitor para contêineres

Quando você configurar o monitoramento do cluster do AKS (Serviço de Kubernetes do Azure) com o Azure Monitor para contêineres, poderá encontrar um problema que impede a coleta de dados ou relatórios de status. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>O Azure Monitor para contêineres está habilitado, mas não relata nenhuma informação
Se o Azure Monitor para contêineres estiver habilitado e configurado com êxito, mas você não conseguir exibir informações de status ou nenhum resultado for retornado de uma consulta de log do Log Analytics, diagnostique o problema seguindo estas etapas: 

1. Verifique o status do agente executando o comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o status de implantação com a versão do agente *06072018* ou posterior usando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o status do pod para verificar se ele está em execução usando o comando: `kubectl get pods --namespace=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, com o status de *Executando* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Verifique os logs de agente. Quando o agente em contêineres é implantado, ele executa uma verificação rápida executando comandos do OMI e mostra a versão do agente e do provedor. 

5. Para verificar se o agente foi integrado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    O status deve ser semelhante a este exemplo:

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela abaixo resume os erros conhecidos que você pode encontrar ao usar o Monitor do Azure para contêineres.

| Mensagens de erro  | Ação |  
| ---- | --- |  
| Mensagem de erro `No data for selected filters`  | Pode levar algum tempo para estabelecer o fluxo de dados de monitoramento para clusters recém-criados. Aguarde pelo menos 10 a 15 minutos para que os dados apareçam para o seu cluster. |   
| Mensagem de erro `Error retrieving data` | Enquanto o cluster do Serviço Azure Kubernetes está sendo configurado para monitoramento de integridade e desempenho, é estabelecida uma conexão entre o cluster e a área de trabalho do Azure Log Analytics. Uma área de trabalho do Log Analytics é usada para armazenar todos os dados de monitoramento do cluster. Este erro pode ocorrer quando sua área de trabalho do Log Analytics for excluída ou perdida. Verifique se o seu espaço de trabalho está disponível, revisando [gerenciar o acesso](../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information). Se o espaço de trabalho estiver ausente, você precisará reintegração seu cluster com o Azure Monitor para contêineres. Para reinserir, você precisará [desativar](/monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json) de monitoramento para o cluster e [habilitar](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) novamente para o Azure Monitor para contêineres. |  
| `Error retrieving data` Depois de adicionar o Azure Monitor para contêineres por meio da cli do az aks | Ao fazer o onboarding usando `az aks cli`, muito raramente, o Azure Monitor para contêineres pode não estar devidamente integrado. Verifique se a solução é integrada. Para fazer isso, vá para a área de trabalho do Log Analytics e veja se a solução está disponível selecionando **Soluções** no painel à esquerda. Para resolver esse problema, você precisará reimplantar a solução seguindo as instruções em [como implantar o Monitor do Azure para contêineres](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

Para ajudar a diagnosticar o problema, fornecemos um script de solução de problemas disponível [aqui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Próximas etapas
Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o Azure Monitor para contêineres, veja [Exibir integridade do Serviço de Kubernetes do Azure](monitoring-container-insights-analyze.md).