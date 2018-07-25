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
ms.date: 07/16/2018
ms.author: magoedte
ms.openlocfilehash: 1fd5ac0f9994a4dbf4365c21ac4f31ba0eccbb15
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069144"
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

- Um cluster AKS novo ou existente
- Um agente do OMS em contêineres para Linux versão microsoft/oms:ciprod04202018 e posteriores. O número de versão é representado por uma data seguindo o formato - *mmddaaaa*.  É instalado automaticamente durante a integração da integridade do contêiner.  
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
Você pode habilitar o monitoramento de um novo cluster AKS durante a implantação do portal do Azure.  Siga as etapas no artigo de início rápido [Implantar um cluster do AKS (Serviço do Kubernetes do Azure)](../aks/kubernetes-walkthrough-portal.md).  Quando você estiver na página **Monitoramento**, selecione **Sim** na opção **Habilitar Monitoramento** para habilitá-lo e selecione um espaço de trabalho do Log Analytics existente ou crie um novo.  

Depois que o monitoramento for habilitado, todas as tarefas de configuração serão concluídas com sucesso e você poderá monitorar o desempenho do cluster de uma destas duas maneiras:

1. Diretamente do cluster do AKS selecionando **Integridade** no painel esquerdo.<br><br> 
2. Clicando no bloco **Monitorar integridade do contêiner** na página de cluster do AKS do cluster selecionado.  No Azure Monitor, selecione **Integridade** no painel esquerdo.  

![Opções para selecionar a integridade do contêiner no AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Depois que o monitoramento for habilitado, poderá levar cerca de 15 minutos até que os dados operacionais para o cluster fiquem visíveis.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Habilitar monitoramento de integridade do contêiner em clusters gerenciados existentes
Você pode habilitar o monitoramento do contêiner do AKS já implantado do portal do Azure ou com o modelo fornecido do Azure Resource Manager usando o cmdlet do PowerShell **New-AzureRmResourceGroupDeployment** ou a Azure CLI.  


### <a name="enable-from-azure-portal"></a>Habilitar a partir do portal do Azure
Execute as etapas a seguir para habilitar o monitoramento do contêiner do AKS no portal do Azure.

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Contêineres**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Serviços de Kubernetes**.<br><br> ![portal do Azure](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. Na lista de contêineres, selecione um contêiner.
3. Na página de visão geral do contêiner, selecione **Monitorar integridade do contêiner** e a página **Integração para logs e integridade do contêiner** aparece.
4. Na página **Integração para logs e integridade do contêiner**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  A lista seleciona previamente o espaço de trabalho e o local padrão no qual o contêiner do AKS está implantado na assinatura.<br><br> ![Habilitar o monitoramento de integridade do contêiner do AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as etapas em [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) e certifique-se de criar o espaço de trabalho na mesma assinatura que é o contêiner AKS implantado.  
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

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implantação do agente e solução
Com a versão do agente *06072018* e superior, você poderá verificar que o agente e a solução foram implantados com êxito.  Com versões anteriores do agente, você só pode verificar a implantação do agente.

### <a name="agent-version-06072018-and-higher"></a>Versão do agente 06072018 e superior
Execute o seguinte comando para verificar se o agente é implantado com êxito.   

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante ao que segue, indicando que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve ser semelhante ao que segue, indicando que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão do agente anterior a 06072018

Para verificar  a versão do agente OMS liberado antes de *06072018* foi implantada adequadamente, execute o comando a seguir:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante ao que segue, indicando que ela foi implantada corretamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Exibir utilização de desempenho
Quando você abre a integridade do contêiner, a página apresenta imediatamente a utilização do desempenho do seu cluster inteiro.  A exibição das informações sobre o cluster do AKS é organizada em quartos perspectivas:

- HDInsight
- Nós 
- Controladores  
- Contêineres

Na guia Cluster, os gráficos de desempenho de linha mostram principais métricas de desempenho do seu cluster.  

![Gráficos de desempenho de exemplo na guia de Cluster](./media/monitoring-container-health/container-health-cluster-perfview.png)

A seguir está uma divisão das métricas de desempenho apresentadas:

- Nó da CPU de % de uso - este gráfico representa uma perspectiva agregada da utilização da CPU para todo o cluster.  Você pode filtrar os resultados para o intervalo de tempo, selecionando *Avg*, *Min*, *Max*, *50th*, *90th* e *95th* no seletor de percentis acima do gráfico, seja individualmente ou combinado. 
- Nó % de uso de memória - este gráfico representa uma perspectiva agregada da utilização da memória para todo o cluster.  Você pode filtrar os resultados para o intervalo de tempo, selecionando *Avg*, *Min*, *Max*, *50th*, *90th* e *95th* no seletor de percentis acima do gráfico, seja individualmente ou combinado. 
- Contagem de nós – este gráfico representa contagem de nós e o status do Kubernetes.  Status de nós do cluster representado são *Todos*, *Pronto*, e *Não está pronto* e pode ser filtrada individualmente ou combinada a partir do seletor acima do gráfico.    
- Contagem do pod de atividade - este gráfico representa status e contagem de pod do Kubernetes.  Status dos pods representados são *Todos*, *Pendente*, *Executando*, e *Desconhecido* e pode ser filtrada individualmente ou combinado da seletor acima do gráfico.  

Alternando para a guia de Nòs, a hierarquia de linhas segue o modelo de objeto Kubernetes, começando com um nó no cluster.  Expanda o nó para ver um ou mais pods em execução no nó; se houver mais de um contêiner agrupado em um pod, eles serão mostrados como a última linha da hierarquia. É possível ver também quantas cargas de trabalho não pod estão em execução no host no caso de o host ter pressão do processador ou memória.

![Exemplo de hierarquia de nós do Kubernetes no modo de exibição de desempenho](./media/monitoring-container-health/container-health-nodes-view.png)

Você pode selecionar controladores ou contêineres na parte superior da página e rever o status e a utilização de recursos desses objetos.  Use as caixas de lista suspensa na parte superior da tela para filtrar por namespace, serviço e nó. Se, em vez disso, você quiser rever a utilização de memória, na lista suspensa **Métrica**, selecione **RSS de Memória** ou **Conjunto de trabalho de memória**.  **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e mais recentes. Caso contrário, você verá valores para **% MIN** mostrando como *NaN %*, que é um valor de tipo de dados numérico que representa um valor indefinido ou não representável. 

![Exibição do desempenho de nós do contêiner](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Por padrão, os dados de desempenho são baseados nas últimas seis horas, mas você pode alterar a janela de tempo na lista suspensa **intervalo de tempo** localizada no canto superior direito da página. Neste momento, a página não é atualizada automaticamente e, portanto, você precisa atualizá-la manualmente. Você também pode filtrar os resultados dentro do intervalo de tempo, selecionando *Avg*, *Min*, *Max*, *50th*, *90th*, and *95th*do seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

No exemplo a seguir, observe que para o nó *aks-agentpool-3977305*, o valor de **Contêineres** é 5, que é a acumulação do número total de contêineres implantado.

![Exemplo de acumulação de contêineres por nó](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Ele pode ajudá-lo a identificar rapidamente se você não tem um número equilibrado de contêineres por nós no cluster.  

A tabela a seguir descreve as informações apresentadas quando você visualiza os nós.

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do host |
| Status | Exibição Kubernetes do status do nó |
| % AVG, % MIN, MAX %, 50%, 90% | Porcentagem média de nós com base no percentil durante a duração do tempo selecionada. |
| AVG, MIN, MAX, 50TH, 90TH | Valor real de nós médio com base no percentil durante a duração de tempo selecionada.  O valor de Média é medido a partir o limite de memória/CPU definido para um nó; no caso de contêineres e pods, é o valor médio relatado pelo host. |
| Contêineres | Número de contêineres. |
| Tempo de atividade | Representa a hora desde que um nó foi iniciado ou reiniciado. |
| Controladores | Somente para os contêineres e pods. Mostra quais são os controladores residentes. Nem todos os pods estarão em um controlador e, portanto, alguns podem mostrar N/D. | 
| Tendência AVG%, MIN%, MAX%, 50TH%, 90TH% | Tendência de gráfico de barras apresentando % de métrica percentil do controlador. |


No seletor, escolha **Controladores**.

![Selecionar modo de exibição de controladores](./media/monitoring-container-health/container-health-controllers-tab.png)

Aqui, você pode ver a integridade do desempenho dos controladores.

![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-health/container-health-controllers-view.png)

A hierarquia de linhas começa com um controlador e expande o controlador e você vê um ou mais contêineres.  Expanda um pod e a última linha mostrará o contêiner agrupado para o pod.  

A tabela a seguir descreve as informações apresentadas quando você visualiza os controladores.

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador|
| Status | Status dos contêineres quando a execução é concluída com status, como *OK*, *Encerrado*, *Com falha* *Parado*, ou *Pausado*. Se o contêiner estiver sendo executado, mas o status não foi devidamente apresentado, ou não foi entendido pelo agente e não respondeu por mais de 30 minutos, o status ficará como *Desconhecido*. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| % AVG, % MIN, MAX %, 50%, 90% | Média de acumulação da porcentagem média de cada entidade para a métrica selecionada e porcentil. |
| AVG, MIN, MAX, 50TH, 90TH  | Acumulação da média de millicore da CPU ou desempenho da memória do contêiner para o percentual selecionado.  O valor de Média é medido a partir do limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador ou pod. |
| Reinícios | Acumulação da contagem de reinicialização dos contêineres. |
| Tempo de atividade | Representa o tempo desde o início de um contêiner. |
| Nó | Somente para os contêineres e pods. Mostra quais são os controladores residentes. | 
| Tendência AVG%, MIN%, MAX%, 50TH%, 90TH%| Tendência de gráfico de barras representando % de métrica percentil do controlador. |

Os ícones no campo status indicam o status online de contêineres:
 
| ícone | Status | 
|--------|-------------|
| ![Ícone de status de execução pronto](./media/monitoring-container-health/container-health-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou em pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/monitoring-container-health/container-health-grey-icon.png) | Última execução relatada, mas sem responder por mais de 30 minutos|
| ![Ícone de status encerrado](./media/monitoring-container-health/container-health-green-icon.png) | Parou com sucesso ou houve falha ao parar|

O ícone de status mostra uma contagem com base no que fornece o pod. Ele mostra os dois estados piores e quando você focaliza o status, mostra um acúmulo de status de todos os pods no contêiner.  Se não houver um estado pronto, o valor de status mostrará um **(0)**.  

No seletor, escolha **Contêineres**.

![Selecionar modo de exibição de contêineres](./media/monitoring-container-health/container-health-containers-tab.png)

Aqui, podemos ver a integridade do desempenho dos contêineres.

![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-health/container-health-containers-view.png)

A tabela a seguir descreve as informações apresentadas quando você visualiza os contêineres.

| Coluna | DESCRIÇÃO | 
|--------|-------------|
| NOME | O nome do controlador|
| Status | Status dos contêineres, se houver. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| % AVG, % MIN, MAX %, 50%, 90% | Média de acumulação da porcentagem média de cada entidade para a métrica selecionada e porcentil. |
| AVG, MIN, MAX, 50TH, 90TH  | Acumulação da média de millicore da CPU ou desempenho da memória do contêiner para o percentual selecionado.  O valor de Média é medido a partir do limite de CPU/memória definido para um pod. |
| Pod | Contêiner no qual reside o pod.| 
| Nó |  Nó em que reside o contêiner. | 
| Reinícios | Representa o tempo desde o início de um contêiner. |
| Tempo de atividade | Representa a hora desde que um contêiner foi iniciado ou reiniciado. |
| Tendência AVG%, MIN%, MAX%, 50TH%, 90TH% | Tendência de gráfico de barras apresentando a porcentagem média de métrica do contêiner. |

Os ícones no campo status indicam o status online de pods:
 
| ícone | Status | 
|--------|-------------|
| ![Ícone de status de execução pronto](./media/monitoring-container-health/container-health-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou em pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/monitoring-container-health/container-health-grey-icon.png) | Última execução relatada, mas sem responder por mais de 30 minutos|
| ![Ícone de status encerrado](./media/monitoring-container-health/container-health-terminated-icon.png) | Parou com sucesso ou houve falha ao parar|
| ![Ícone de status com falha](./media/monitoring-container-health/container-health-failed-icon.png) | Estado com falha |

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

Você pode executar análises interativas dos dados no espaço de trabalho selecionando a opção **Exibir Log**, disponível na extremidade direita quando um contêiner ou controlador é expandido.  A página **Pesquisa de Logs** aparece logo acima da página em que você estava no portal.

![Análise de dados no Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

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

4. Edite o valor de **aksResourceId** e **aksResourceLocation** com os valores do cluster do AKS, que podem ser encontrados na página **Propriedades** do cluster selecionado.

    ![Página de propriedades do contêiner](./media/monitoring-container-health/container-properties-page.png)

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

1. Verifique o status do agente executando o seguinte comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve ser semelhante ao que segue, indicando que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o status de implantação de solução com a versão do agente *06072018* ou superior, executando o seguinte comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve ser semelhante ao que segue, indicando que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique a execução do pod por meio do seu status executando o seguinte comando: `kubectl get pods --namespace=kube-system`

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

4. Verifique os logs de agente. Quando o agente em contêineres é implantado, ele executa uma verificação rápida executando comandos do OMI e mostrando a versão do agente e 
5.  provedor. Para ver se o agente foi integrado com êxito, execute o seguinte comando: `kubectl logs omsagent-484hw --namespace=kube-system`

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