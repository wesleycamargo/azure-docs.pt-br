---
title: Monitorar a integridade do AKS (Serviço do Kubernetes do Azure) (Versão prévia) | Microsoft Docs
description: Este artigo descreve como você pode rever o desempenho do contêiner do AKS para entender rapidamente como o ambiente hospedado do Kubernetes é usado.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2018
ms.author: magoedte
ms.openlocfilehash: 7c4294947cba72b1638e77c2dd8de1f5ee37b62a
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285977"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorar a integridade do contêiner do AKS (Serviço do Kubernetes do Azure) (Versão prévia)

Este artigo descreve como configurar e usar a integridade de contêiner do Azure Monitor para monitorar o desempenho das cargas de trabalho implantadas em ambientes do Kubernetes hospedados no AKS (serviço do Kubernetes do Azure).  Monitorar seu cluster do Kubernetes e os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

A integridade do contêiner oferece capacidade de monitoramento de desempenho com a coleta de memória e métricas de processador de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas.  Depois de habilitar a integridade de contêiner, essas métricas serão coletadas automaticamente com uma versão em contêineres do Agente do OMS para Linux e armazenadas em seu espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md).  As exibições predefinidas incluídas mostram as cargas de trabalho do contêiner residentes e o que está afetando a integridade do desempenho do cluster Kubernetes para que você possa entender:  

* Quais contêineres estão em execução no nó e a média de utilização do processador e da memória para identificar afunilamentos de recursos
* Identificar onde o contêiner reside em um controlador e/ou em pods para ver o desempenho geral de um controlador ou pod 
* Rever a utilização de recursos de cargas de trabalho em execução no host não relacionadas aos processos padrão que dão suporte ao pod
* Compreender o comportamento do cluster com carga média e com carga mais pesada para ajudar a identificar as necessidades de capacidade e determinar a carga máxima que ele pode suportar 

Se você estiver interessado em monitorar e gerenciar hosts de contêiner Docker e Windows para exibir configuração, auditoria e utilização de recursos, consulte a [Solução de Monitoramento de Contêiner](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Requisitos 
Antes de começar, examine os detalhes a seguir para compreender os pré-requisitos com suporte.

- As seguintes versões do cluster do AKS têm suporte: 1.7.7 a 1.9.6.
- Um agente do OMS em contêineres para Linux versão microsoft/oms:ciprod04202018 e posteriores. Esse agente é instalado automaticamente durante a integração da integridade do contêiner.  
- Um espaço de trabalho do Log Analytics.  Ele pode ser criado quando você habilita o monitoramento do novo cluster do AKS, ou você pode criar um por meio do [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), do [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou do [portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Membro da função Colaborador do Log Analytics para habilitar o monitoramento de contêineres.  Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerenciar espaços de trabalho](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Componentes 

Esse recurso depende da coleta de dados de eventos e desempenho, por um Agente do OMS em contêineres para Linux, de todos os nós no cluster.  O agente é automaticamente implantado e registrado no espaço de trabalho do Log Analytics especificado depois que o monitoramento de contêiner é habilitado. 

>[!NOTE] 
>Se você já tiver implantado um cluster do AKS, habilite o monitoramento usando um modelo do Azure Resource Manager fornecido, conforme demonstrado neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente.  
>

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Habilitar monitoramento de integridade de contêiner em um novo cluster
Você só pode habilitar o monitoramento do cluster do AKS quando o implanta no portal do Azure.  Siga as etapas no artigo de início rápido [Implantar um cluster do AKS (Serviço do Kubernetes do Azure)](../aks/kubernetes-walkthrough-portal.md).  Quando você estiver na página **Monitoramento**, selecione **Sim** na opção **Habilitar Monitoramento** para habilitá-lo e selecione um espaço de trabalho do Log Analytics existente ou crie um novo.  

Depois que o monitoramento for habilitado, todas as tarefas de configuração serão concluídas com sucesso e você poderá monitorar o desempenho do cluster de uma destas duas maneiras:

1. Diretamente do cluster do AKS selecionando **Integridade** no painel esquerdo.<br><br> 
2. Clicando no bloco **Monitorar integridade do contêiner** na página de cluster do AKS do cluster selecionado.  No Azure Monitor, selecione **Integridade** no painel esquerdo.  

![Opções para selecionar a integridade do contêiner no AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Depois que o monitoramento for habilitado, poderá levar cerca de 15 minutos até que os dados operacionais para o cluster fiquem visíveis.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Habilitar monitoramento de integridade do contêiner em clusters gerenciados existentes
A habilitação do monitoramento do contêiner do AKS já implantado pode ser realizada no portal do Azure ou com o modelo fornecido do Azure Resource Manager usando o cmdlet do PowerShell **New-AzureRmResourceGroupDeployment** ou a Azure CLI.  


### <a name="enable-from-azure-portal"></a>Habilitar a partir do portal do Azure
Execute as etapas a seguir para habilitar o monitoramento do contêiner do AKS no portal do Azure.

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Contêineres**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Serviços de Kubernetes**.<br><br> ![Portal do Azure](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. Na lista de contêineres, selecione um contêiner.
3. Na página de visão geral do contêiner, selecione **Monitorar integridade do contêiner** e a página **Integração para logs e integridade do contêiner** aparece.
4. Na página **Integração para logs e integridade do contêiner**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  A lista seleciona previamente o espaço de trabalho e o local padrão no qual o contêiner do AKS está implantado na assinatura. Ou você pode selecionar **Criar Novo** e especificar um novo espaço de trabalho na mesma assinatura.<br><br> ![Habilitar o monitoramento de integridade do contêiner do AKS](./media/monitoring-container-health/container-health-enable-brownfield.png) 

    Se você selecionar **Criar Novo**, o painel **Criar novo espaço de trabalho** aparece. A **Região** é padronizada para a região em que o recurso de contêiner é criado e você pode aceitar o padrão ou selecionar uma região diferente e, em seguida, especificar um nome para o espaço de trabalho.  Clique em **Criar** para aceitar a sua seleção.<br><br> ![Definir espaço de trabalho para monitoramento de contêiner](./media/monitoring-container-health/create-new-workspace-01.png)  

    >[!NOTE]
    >No momento, não é possível criar um novo espaço de trabalho na região do Centro-Oeste dos EUA, somente é possível selecionar um espaço de trabalho existente nessa região.  Mesmo que você possa selecionar essa região na lista, a implantação será iniciada, mas falhará pouco tempo depois.  
    >
 
Depois que o monitoramento for habilitado, poderá levar cerca de 15 minutos até que os dados operacionais para o cluster fiquem visíveis. 

### <a name="enable-using-azure-resource-manager-template"></a>Habilitar usando modelo do Azure Resource Manager
Esse método inclui dois modelos JSON, um modelo especifica a configuração para habilitar o monitoramento e o outro modelo JSON contém valores de parâmetros que você configura para especificar o seguinte:

* ID de recurso do contêiner do AKS 
* Grupo de recursos em que o cluster foi implantado 
* Espaço de trabalho do Log Analytics e região onde o espaço de trabalho será criado 

O espaço de trabalho do Log Analytics deve ser criado manualmente.  Para criar o espaço de trabalho, você pode configurá-lo por meio do [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), do [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) e do [portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Se você não estiver familiarizado com os conceitos de implantação de recursos usando um modelo com o PowerShell, confira [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)ou, para a CLI do Azure, confira [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente.  É necessário executar a versão da CLI do Azure 2.0.27 ou posterior. Execute `az --version` para identificar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Criar e executar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. Salve esse arquivo como **existingClusterOnboarding.json** em uma pasta local.
3. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Edite o valor de **aksResourceId** e **aksResourceLocation** com os valores que você pode encontrar na página **Visão geral do AKS** para o cluster do AKS.  O valor para **workspaceResourceId** é a ID do recurso completa do espaço de trabalho do Log Analytics, que inclui o nome do espaço de trabalho.  Especifique também o local do espaço de trabalho para **workspaceRegion**.    
5. Salve esse arquivo como **existingClusterParam.json** em uma pasta local.
6. Você está pronto para implantar o modelo. 

    * Use os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Para executar o seguinte comando com a CLI do Azure no Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

        ```azurecli
        provisioningState       : Succeeded
        ```
Depois que o monitoramento for habilitado, poderá levar cerca de 15 minutos até que os dados operacionais para o cluster fiquem visíveis.  

## <a name="verify-agent-deployed-successfully"></a>Verifique se o agente fez a implantação com êxito
Para verificar se o agente do OMS foi implantado corretamente, execute o seguinte comando: ` kubectl get ds omsagent --namespace=kube-system`.

A saída deve ser semelhante ao que segue, indicando que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Exibir utilização de desempenho
Quando você abre a integridade do contêiner, a página apresenta imediatamente a utilização do desempenho dos nós de cluster.  A exibição das informações sobre o cluster do AKS é organizada em três perspectivas:

- Nós 
- Controladores  
- Contêineres

A hierarquia de linhas segue o modelo de objeto Kubernetes, começando com um nó no cluster.  Expanda o nó para ver um ou mais pods em execução no nó; se houver mais de um contêiner agrupado em um pod, eles serão mostrados como a última linha da hierarquia.<br><br> ![Exemplo de hierarquia de nós do Kubernetes no modo de exibição de desempenho](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Você pode selecionar controladores ou contêineres na parte superior da página e rever o status e a utilização de recursos desses objetos.  Use as caixas de lista suspensa na parte superior da tela para filtrar por namespace, serviço e nó. Se, em vez disso, você quiser rever a utilização de memória, na lista suspensa **Métrica**, selecione **RSS de Memória** ou **Conjunto de trabalho de memória**.  **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e mais recentes. Caso contrário, você verá valores para **% MÉDIA** aparecendo como *NaN %*, que é um valor de tipo de dados numérico que representa um valor indefinido ou que não pode ser representado. 

![Exibição de desempenho de nós de desempenho do contêiner](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Por padrão, os dados de desempenho são baseados nas últimas seis horas, mas você pode alterar a janela de tempo na lista suspensa **intervalo de tempo** localizada no canto superior direito da página. Neste momento, a página não é atualizada automaticamente e, portanto, você precisa atualizá-la manualmente. 

No exemplo a seguir, observe que para o nó *aks-agentpool-3402399-0*, o valor de **Containers** é 10, que é a acumulação do número total de contêineres implantado.<br><br> ![Exemplo de acumulação de contêineres por nó](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Ele pode ajudá-lo a identificar rapidamente se você não tem um número equilibrado de contêineres por nós no cluster.  

A tabela a seguir descreve as informações apresentadas quando você visualiza os nós.

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do host |
| Status | Exibição Kubernetes do status do nó |
| % MÉDIA | Porcentagem média de nós com base na métrica selecionada pela duração de tempo selecionada. |
| MÉDIA | Valor real da média de nós com base na métrica selecionada pela duração de tempo selecionada.  O valor de Média é medido a partir o limite de memória/CPU definido para um nó; no caso de contêineres e pods, é o valor médio relatado pelo host. |
| Contêineres | Número de contêineres. |
| Tempo de atividade | Representa a hora desde que um nó foi iniciado ou reiniciado. |
| Pod | Somente para contêineres. Mostra quais são os pods residentes. |
| Controladores | Somente para os contêineres e pods. Mostra quais são os controladores residentes. Nem todos os pods estarão em um controlador e, portanto, alguns podem mostrar N/D. | 
| % média de tendência | Tendência de gráfico de barras com base na porcentagem de métrica da média de nós e contêineres. |


No seletor, escolha **Controladores**.<br><br> ![Selecionar modo de exibição de controladores](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Aqui, você pode ver a integridade do desempenho dos controladores.<br><br> ![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-health/container-performance-and-health-view-05.png)

A hierarquia de linhas começa com um controlador e expande o controlador, e você vê um ou mais pods ou um ou mais contêineres.  Expanda um pod e a última linha mostrará o contêiner agrupado para o pod.  

A tabela a seguir descreve as informações apresentadas quando você visualiza os controladores.

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador|
| Status | Status dos contêineres quando a execução é concluída com status, como *Terminado*, *Com Falha* *Parado* ou *Em Pausa*. Se o contêiner está sendo executado, mas o status não foi devidamente apresentado, ou não foi entendido pelo agente e não respondeu por mais de 30 minutos, o status ficará como *Desconhecido*. |
| % MÉDIA | Média de acumulação da porcentagem média de cada entidade para a métrica selecionada. |
| MÉDIA | Acumulação da média de millicore da CPU ou desempenho da memória do contêiner.  O valor de Média é medido a partir do limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador ou pod. |
| Reinícios | Acumulação da contagem de reinicialização dos contêineres. |
| Tempo de atividade | Representa o tempo desde o início de um contêiner. |
| Pod | Somente para contêineres. Mostra quais são os pods residentes. |
| Nó | Somente para os contêineres e pods. Mostra quais são os controladores residentes. | 
| % média de tendência | Tendência de gráfico de barras apresentando a porcentagem média de métrica do contêiner. |

No seletor, escolha **Contêineres**.<br><br> ![Selecionar modo de exibição de contêineres](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Aqui, podemos ver a integridade do desempenho dos contêineres.<br><br> ![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-health/container-performance-and-health-view-06.png)

A tabela a seguir descreve as informações apresentadas quando você visualiza os contêineres.

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador|
| Status | Status de acumulação dos contêineres, se houver. |
| % MÉDIA | Média de acumulação da porcentagem média de cada entidade para a métrica selecionada. |
| MÉDIA | Acumulação da média de millicore da CPU ou desempenho da memória do contêiner. O valor de Média é medido a partir do limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador.|
| Reinícios | Representa o tempo desde o início de um contêiner. |
| Tempo de atividade | Representa a hora desde que um contêiner foi iniciado ou reiniciado. |
| Pod | Informações do pod de onde ele reside. |
| Nó |  Nó em que reside o contêiner.  | 
| % média de tendência | Tendência de gráfico de barras apresentando a porcentagem média de métrica do contêiner. |

## <a name="container-data-collection-details"></a>Detalhes da coleta de dados dos contêineres
A integridade do contêiner coleta vários dados de log e métricas de desempenho de contêineres e hosts de contêiner. Os dados são coletados a cada três minutos.

### <a name="container-records"></a>Registros de contêiner

A tabela a seguir mostra exemplos de registros coletados pela integridade do contêiner e os tipos de dados que aparecem nos resultados da pesquisa de log.

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
O Log Analytics pode ajudá-lo a procurar por tendências, diagnosticar afunilamentos, prever ou correlacionar dados que podem ajudar a determinar se a configuração do cluster atual está sendo executada corretamente.  Pesquisas de logs predefinidas são fornecidas para uso imediato ou para personalização a fim de retornar as informações como você desejar. 

Você pode executar análises interativas dos dados no espaço de trabalho selecionando a opção **Exibir Log**, disponível na extremidade direita quando um contêiner é expandido.  A página **Pesquisa de Logs** aparece logo acima da página em que você estava no portal.<br><br> ![Analisar dados no Log Analytics](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

A saída dos logs do contêiner encaminhada para o Log Analytics são STDOUT e STDERR. Como a integridade de contêiner está monitorando o AKS (Serviço do Kubernetes do Azure), Kube-system não é coletado atualmente devido ao grande volume de dados gerado.     

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log
Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-los de acordo com seus requisitos. Você pode testar as consultas de exemplo a seguir para criar consultas mais avançadas.

| Consultar | DESCRIÇÃO | 
|-------|-------------|
| ContainerInventory<br> &#124; projetar Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;processar tabela | Listar todas as informações do ciclo de vida do contêiner| 
| KubeEvents_CL<br> &#124; em que not(isempty(Namespace_s))<br> &#124; classificar por TimeGenerated desc<br> &#124;processar tabela | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; resumir AggregatedValue = count() by Image, ImageTag, Running | Inventário de imagem | 
| **Em Análise Avançada, selecione gráficos de linhas**:<br> Perf<br> &#124; em que ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; resumir AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU do contêiner | 
| **Em Análise Avançada, selecione gráficos de linhas**:<br> Perf &#124; em que ObjectName == "Container" e CounterName == "Memory Usage MB"<br> &#124; resumir AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memória de contêiner |

## <a name="how-to-stop-monitoring-with-container-health"></a>Como interromper o monitoramento com a integridade de contêiner
Depois de habilitar o monitoramento do contêiner do AKS, se você não desejar monitorá-lo, poderá escolher *recusar* usando os modelos do Azure Resource Manager fornecidos com o cmdlet do PowerShell **New-AzureRmResourceGroupDeployment** ou a CLI do Azure.  Um modelo JSON especifica a configuração de *recusa* e o outro modelo JSON contém valores de parâmetros que você configura para especificar a ID de recurso e o grupo de recursos do cluster do AKS em que o cluster foi implantado.  Se você não estiver familiarizado com os conceitos de implantação de recursos usando um modelo com o PowerShell, confira [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)ou, para a CLI do Azure, confira [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente.  É necessário executar a versão da CLI do Azure 2.0.27 ou posterior. Execute `az --version` para identificar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Criar e executar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Salve esse arquivo como **OptOutTemplate.json** em uma pasta local.
3. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Edite o valor de **aksResourceId** e **aksResourceLocation** com os valores do cluster do AKS, que podem ser encontrados na página **Propriedades** do cluster selecionado.<br><br> ![Página de propriedades do contêiner](./media/monitoring-container-health/container-properties-page.png)<br>

    Enquanto você estiver na página **Propriedades**, copie também a **ID do Recurso de Espaço de Trabalho**.  Esse valor será necessário se você decidir que deseja excluir o espaço de trabalho do Log Analytics mais tarde, o que não é feito como parte deste processo.  

5. Salve esse arquivo como **OptOutParam.json** em uma pasta local.
6. Você está pronto para implantar o modelo. 

    * Para usar os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        A alteração da configuração pode levar alguns minutos para ser concluída. Quando concluir, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Para executar o seguinte comando com a CLI do Azure no Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        A alteração da configuração pode levar alguns minutos para ser concluída. Quando concluir, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Se o espaço de trabalho foi criado apenas para dar suporte ao monitoramento do cluster e não for mais necessário, você terá que excluí-lo manualmente. Se você não souber como excluir um espaço de trabalho, confira [Excluir um espaço de trabalho do Azure Log Analytics com o portal do Azure](../log-analytics/log-analytics-manage-del-workspace.md).  Não se esqueça da **ID do Recurso de Espaço de Trabalho** copiada anteriormente na etapa 4; você precisará dela.  

## <a name="troubleshooting"></a>solução de problemas
Esta seção fornece informações para ajudar a solucionar problemas com a integridade do contêiner.

Se a integridade do contêiner foi habilitada e configurada com êxito, mas você não vê todas as informações de status ou resultados do Log Analytics ao executar uma pesquisa de logs, execute as etapas a seguir para ajudar a diagnosticar o problema.   

1. Verifique o status do agente executando o seguinte comando: `kubectl get ds omsagent --namespace=kube-system`

    A saída deve ser semelhante ao que segue, indicando que o agente está em execução em todos os nós no cluster.  Por exemplo, esse cluster tem dois nós e você deve esperar que o valor para essa quantidade de nós.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Verifique a execução do pod por meio do seu status executando o seguinte comando: `kubectl get pods --namespace=kube-system`

    A saída deve ser semelhante ao seguinte, com o status de *Executando* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. Verifique os logs de agente. Quando o agente em contêineres é implantado, ele executa uma verificação rápida executando comandos do OMI e mostrando a versão do agente e o provedor do Docker. Para ver se o agente foi integrado com êxito, execute o seguinte comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    O status deve ser semelhante a este:

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

## <a name="next-steps"></a>Próximas etapas

[Pesquise logs](../log-analytics/log-analytics-log-search.md) para exibir informações detalhadas de desempenho do aplicativo e de integridade do contêiner.  
