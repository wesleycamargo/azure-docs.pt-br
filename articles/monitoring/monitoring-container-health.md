---
title: Monitorar a integridade do AKS (Serviço do Kubernetes do Azure) (versão prévia) | Microsoft Docs
description: Este artigo descreve como você pode rever o desempenho do contêiner do AKS para entender rapidamente como o ambiente hospedado do Kubernetes é usado.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: magoedte
ms.openlocfilehash: caf290477a4fd4f03bb248cc89f91027dbe68f3e
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382440"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorar a integridade do contêiner do AKS (Serviço do Kubernetes do Azure) (versão prévia)

Este artigo descreve como configurar e usar a integridade de contêiner do Azure Monitor para monitorar o desempenho das cargas de trabalho implantadas em ambientes do Kubernetes e hospedadas no AKS (serviço do Kubernetes do Azure). Monitorar os contêineres e clusters do Kubernetes é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

A integridade do contêiner oferece capacidade de monitoramento de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. Depois de ativar a integridade do contêiner, essas métricas são coletadas automaticamente para você por meio de uma versão em contêiner do agente do Log Analytics para Linux e armazenadas em sua área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md). Os modos de exibição predefinidos incluídos mostram as cargas de trabalho de contêiner residentes e o que afeta a integridade do desempenho do cluster do Kubernetes para que você possa:  

* Identificar contêineres que estão em execução no nó e sua utilização média de processador e memória. Esse conhecimento pode ajudá-lo a identificar gargalos de recursos.
* Identificar em que local o contêiner reside em um controlador ou em um pod. Esse conhecimento pode ajudá-lo a exibir o desempenho geral do controlador ou do pod. 
* Examine a utilização de recursos de cargas de trabalho em execução no host não relacionadas aos processos padrão que dão suporte ao pod.
* Compreender o comportamento do cluster sob cargas mais pesadas e médias. Esse conhecimento pode ajudá-lo a identificar as necessidades de capacidade e determinar a carga máxima que o cluster pode sustentar. 

Se você estiver interessado em monitorar e gerenciar hosts de contêiner Docker e Windows para exibir configuração, auditoria e utilização de recursos, consulte a [Solução de Monitoramento de Contêiner](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Pré-requisitos 
Antes de começar, verifique se você tem o seguinte:

- Um cluster AKS novo ou existente.
- Um agente do Log Analytics em contêineres para a versão do Linux microsoft / oms:ciprod04202018 ou posterior. O número de versão é representado por uma data no formato a seguir: *mmddaaaa*. Esse agente é instalado automaticamente durante a integração da integridade do contêiner. 
- Um espaço de trabalho do Log Analytics. É possível criá-lo ao habilitar o monitoramento do novo cluster do AKS ou permitir que a experiência de integração crie um espaço de trabalho padrão no grupo de recursos padrão da assinatura de cluster do AKS. Se optar por criá-lo por conta própria, será possível criá-lo por meio do [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), do [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- A função de colaborador do Log Analytics, para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Componentes 

Sua capacidade de monitorar o desempenho depende de um agente de Log Analytics contido no container para Linux, que coleta dados de desempenho e de eventos de todos os nós no cluster. O agente é automaticamente implantado e registrado no espaço de trabalho do Log Analytics especificado depois que o monitoramento de contêiner é habilitado. 

>[!NOTE] 
>Se você já implantou um cluster do AKS, habilite o monitoramento usando a CLI do Azure ou um modelo do Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente. 
>

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Entre no [Portal do Azure](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Habilitar monitoramento de integridade de contêiner em um novo cluster
Durante a implantação, você poderá habilitar o monitoramento de um novo cluster do AKS no portal do Azure ou com a CLI do Azure. Siga as etapas no artigo de início rápido [Implantar um cluster do AKS (Serviço de Kubernetes do Azure)](../aks/kubernetes-walkthrough-portal.md), se quiser habilitar a partir do portal. Na página **Monitoramento**, para a opção **Habilitar Monitoramento**, selecione **Sim** e, em seguida, selecione um espaço de trabalho do Log Analytics existente ou crie um novo. 

Para habilitar o monitoramento de um novo cluster do AKS criado com a CLI do Azure, siga a etapa no artigo de início rápido na seção [Criar cluster do AKS](../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.43 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

Depois de habilitar o monitoramento e todas as tarefas de configuração terem sido concluídas com sucesso, você poderá monitorar o desempenho do cluster de uma destas duas maneiras:

* Diretamente no cluster do AKS selecionando **Integridade** no painel esquerdo.
* Selecionando o bloco **Monitorar integridade do contêiner** na página de cluster do AKS do cluster selecionado. No Azure Monitor, no painel esquerdo, selecione **Integridade**. 

  ![Opções para selecionar a integridade do contêiner no AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir dados operacionais para o cluster. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Habilitar monitoramento de integridade do contêiner em clusters gerenciados existentes
É possível habilitar o monitoramento de um cluster do AKS que já tenha sido implantado usando a CLI do Azure, a partir do portal ou com o modelo do Azure Resource Manager fornecido, usando o cmdlet `New-AzureRmResourceGroupDeployment` do PowerShell. 

### <a name="enable-monitoring-using-azure-cli"></a>Habilitar o monitoramento usando a CLI do Azure
A etapa a seguir permite o monitoramento do cluster do AKS usando a CLI do Azure. Neste exemplo, não é necessário criar ou especificar um espaço de trabalho existente. Esse comando simplificará o processo, criando um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster do AKS, se ainda não existir um na região.  O espaço de trabalho padrão criado é semelhante ao formato do *DefaultWorkspace-<GUID>-<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A saída será semelhante à seguinte:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-in-the-azure-portal"></a>Habilitar o monitoramento no portal do Azure
Para habilitar o monitoramento do seu contêiner AKS no portal do Azure, faça o seguinte:

1. No portal do Azure, clique em **Todos os serviços**. 
2. Na lista de recursos, comece digitando **Contêineres**.  
    A lista filtra com base na sua entrada. 
3. Selecione **Serviços de Kubernetes**.  

    ![O link de serviços do Kubernetes](./media/monitoring-container-health/azure-portal-01.png)

4. Na lista de contêineres, selecione um contêiner.
5. Na página de visão geral do contêiner, selecione **Monitorar integridade do contêiner**.  
6. Na página **Integração para Logs e Integridade do Contêiner**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura que o cluster, selecione-o na lista suspensa.  
    A lista seleciona previamente o espaço de trabalho e o local padrão no qual o contêiner do AKS está implantado na assinatura. 

    ![Habilitar o monitoramento de integridade do contêiner do AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Crie o espaço de trabalho na mesma assinatura em que o contêiner AKS é implantado. 
 
Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir dados operacionais para o cluster. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Habilitar o monitoramento usando um modelo do Azure Resource Manager
Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

* A ID de recurso do contêiner do AKS. 
* O grupo de recursos no qual o cluster está implantado.
* O espaço de trabalho do Log Analytics e a região na qual criar o espaço de trabalho. 

O espaço de trabalho do Log Analytics deve ser criado manualmente. Para criar o espaço de trabalho, você pode configurá-lo por meio do [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), por meio do [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

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
3. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
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

4. Edite os valores para **aksResourceId** e **aksResourceLocation** usando os valores na página **Visão Geral do AKS** para o cluster do AKS. O valor para **workspaceResourceId** é a ID do recurso completa do espaço de trabalho do Log Analytics, que inclui o nome do espaço de trabalho. Especifique também o local do espaço de trabalho para **workspaceRegion**. 
5. Salve esse arquivo como **existingClusterParam.json** em uma pasta local.
6. Você está pronto para implantar o modelo. 

    * Use os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Para executar o comando a seguir usando a CLI do Azure no Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

        ```azurecli
        provisioningState       : Succeeded
        ```
Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir dados operacionais para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implantação do agente e solução
Com a versão do agente *06072018* ou posterior, você pode verificar se tanto o agente quanto a solução foram implantados com êxito. Com versões anteriores do agente, você pode verificar somente a implantação do agente.

### <a name="agent-version-06072018-or-later"></a>Agente versão 06072018 ou posterior
Execute o seguinte comando para verificar se o agente foi implantado com êxito. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão do agente anterior a 06072018

Para verificar se a versão do agente do Log Analytics liberada antes de *06072018* está implementada corretamente, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Exibir configuração com CLI
Use o comando `aks show` para obter detalhes, como se a solução estivesse habilitada ou não, o que é o resourceID do espaço de trabalho do Log Analytics e detalhes de resumo sobre o cluster.  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando concluirá e retornará informações no formato JSON sobre a solução.  Os resultados do comando deverão mostrar o perfil do complemento de monitoramento e serem semelhantes à seguinte saída de exemplo:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="view-performance-utilization"></a>Exibir utilização de desempenho
Quando você abre a integridade do contêiner, a página apresenta imediatamente a utilização do desempenho do seu cluster inteiro. A exibição das informações sobre o cluster do AKS é organizada em quartos perspectivas:

- HDInsight
- Nós 
- Controladores  
- Contêineres

Na guia **Cluster**, os gráficos de desempenho de quatro linhas mostram as principais métricas de desempenho do seu cluster. 

![Gráficos de desempenho de exemplo na guia de Cluster](./media/monitoring-container-health/container-health-cluster-perfview.png)

O gráfico de desempenho exibe quatro métricas de desempenho:

- **Utilização da CPU do Nó&nbsp;%**: uma perspectiva agregada da utilização da CPU para todo o cluster. Você pode filtrar os resultados para o intervalo de tempo selecionando **Méd.**, **Mín.**, **Máx.**, **50º**, **90º** e **95º** no seletor de percentis acima do gráfico, sejam individualmente ou combinados. 
- **Utilização de memória do nó&nbsp;%**: uma perspectiva agregada da utilização de memória para todo o cluster. Você pode filtrar os resultados para o intervalo de tempo selecionando **Méd.**, **Mín.**, **Máx.**, **50º**, **90º** e **95º** no seletor de percentis acima do gráfico, sejam individualmente ou combinados. 
- **Contagem de nós**: uma contagem de nós e o status do Kubernetes. Os status de nós do cluster representado são *Todos*, *Pronto*, e *Não pronto*, e podem ser filtrados individualmente ou de modo combinado no seletor acima do gráfico. 
- **Contagem de pods de atividade**: contagem de pods e status do Kubernetes. Os status dos pods representados são *Todos*, *Pendente*, *Em execução* e *Desconhecido* e podem ser filtrados individualmente ou de modo combinado no seletor acima do gráfico. 

Quando você alterna para os **Nós**, **Controladores**, e guia **Contêineres**, exibido automaticamente no lado direito da página está o painel de propriedade.  Ele mostra as propriedades do item selecionado, incluindo rótulos que você definir para organizar objetos Kubernetes.  Clique no **>>** link no painel de visualizar\ocultar o painel.  

![Exemplo do painel de propriedades de perspectivas de Kubernetes](./media/monitoring-container-health/perspectives-preview-pane-01.png)

Como expandir os objetos na hierarquia, as atualizações de painel de propriedades com base no objeto selecionado. No painel também pode exibir eventos de Kubernetes com pesquisas de log predefinidas, clicando no link dos **logs de eventos de Kubernetes do modo de exibição** na parte superior do painel. Para obter informações adicionais sobre como exibir dados de log do Kubernetes, consulte [Pesquisar os logs para analisar dados](#search-logs-to-analyze-data).

Alterne para a guia **Nós**, a hierarquia de linhas seguirá o modelo de objeto do Kubernetes começando com um nó no cluster. Expanda o nó e poderá exibir um ou mais pods em execução no nó. Se mais de um contêiner for agrupado em um pod, eles serão exibidos como a última linha na hierarquia. Você também poderá exibir quantas cargas de trabalho não relacionadas a pod estão em execução no host se o host tiver pressão de memória ou processador.

![Exemplo de hierarquia de nós do Kubernetes no modo de exibição de desempenho](./media/monitoring-container-health/container-health-nodes-view.png)

Você pode selecionar controladores ou contêineres na parte superior da página e examinar o status e a utilização de recursos para esses objetos. Use as caixas suspensas na parte superior para filtrar por namespace, serviço e nó. Se, em vez disso, você quiser examinar a utilização de memória, na lista suspensa **Métrica**, selecione **RSS de Memória** ou **Conjunto de trabalho de memória**. **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e posteriores. Caso contrário, você exibirá valores para **Mín.&nbsp;%** como *NaN&nbsp;%*, que é um valor de tipo de dados numérico que representa um valor indefinido ou não representável. 

![Exibição do desempenho de nós do contêiner](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Por padrão, os dados de Desempenho são baseados nas últimas seis horas, mas você pode alterar a janela usando a lista suspensa **Intervalo de Tempo** na parte superior direita. Neste momento, a página não é atualizada automaticamente e, portanto, você precisa atualizá-la manualmente. Você também pode filtrar os resultados dentro do intervalo de tempo selecionando **Méd.**, **Mín.**, **Máx.**, **50º**, **90º** e **95º** no seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

No exemplo a seguir, observe que, para o nó *aks-nodepool-3977305*, o valor de **Contêineres** é 5, que é a acumulação do número total de contêineres implantados.

![Exemplo de acumulação de contêineres por nó](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

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

![Selecionar modo de exibição de controladores](./media/monitoring-container-health/container-health-controllers-tab.png)

Aqui, você pode exibir a integridade do desempenho de seus controladores.

![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-health/container-health-controllers-view.png)

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
| ![Ícone de status de execução pronto](./media/monitoring-container-health/container-health-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou em pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/monitoring-container-health/container-health-grey-icon.png) | Última execução relatada, mas sem responder por mais de 30 minutos|
| ![Ícone de status de êxito](./media/monitoring-container-health/container-health-green-icon.png) | Parou com sucesso ou houve falha ao parar|

O ícone de status mostra uma contagem com base no que o pod fornece. Ele mostra os dois piores estados e, quando você focaliza o status, mostra um status acumulado de todos os pods no contêiner. Se não houver um estado pronto, o valor de status mostrará **(0)**. 

No seletor, selecione **Contêineres**.

![Selecionar modo de exibição de contêineres](./media/monitoring-container-health/container-health-containers-tab.png)

Aqui você pode exibir a integridade do desempenho de seus contêineres.

![Exibição de desempenho dos controladores de <Name>](./media/monitoring-container-health/container-health-containers-view.png)

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
| ![Ícone de status de execução pronto](./media/monitoring-container-health/container-health-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou em pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/monitoring-container-health/container-health-grey-icon.png) | Relatado pela última vez como em execução, mas sem responder por mais de 30 minutos|
| ![Ícone de status encerrado](./media/monitoring-container-health/container-health-terminated-icon.png) | Parou com sucesso ou houve falha ao parar|
| ![Ícone de status com falha](./media/monitoring-container-health/container-health-failed-icon.png) | Estado com falha |

## <a name="container-data-collection-details"></a>Detalhes da coleta de dados dos contêineres
A integridade do contêiner coleta vários dados de log e métricas de desempenho de contêineres e hosts de contêiner. Os dados são coletados a cada três minutos.

### <a name="container-records"></a>Registros de contêiner

Exemplos de registros coletados por integridade do contêiner e tipos de dados que aparecem nos resultados da pesquisa de logs são exibidos na tabela a seguir:

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

Você pode executar análises interativas dos dados no espaço de trabalho selecionando a opção **Exibir logs de evento Kubernetes** ou **Visualizar logs de** contêiner no painel de visualização. A página **Pesquisa de Logs** aparece acima da página de portal do Azure em que você estava.

![Análise de dados no Log Analytics](./media/monitoring-container-health/container-health-log-search-example.png)   

As saídas dos logs do contêiner encaminhada para o Log Analytics são STDOUT e STDERR. Uma vez que a integridade de contêiner está monitorando o Kubernetes gerenciados pelo Azure (AKS), Kube-system não é coletado hoje devido ao grande volume de dados gerado. 

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log
Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-las de acordo com seus requisitos. Para ajudar a criar consultas mais avançadas, você pode experimentar as seguintes consultas de amostra:

| Consultar | DESCRIÇÃO | 
|-------|-------------|
| ContainerInventory<br> &#124; projetar Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;processar tabela | Listar todas as informações do ciclo de vida do contêiner| 
| KubeEvents_CL<br> &#124; em que not(isempty(Namespace_s))<br> &#124; classificar por TimeGenerated desc<br> &#124;processar tabela | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; resumir AggregatedValue = count() by Image, ImageTag, Running | Inventário de imagem | 
| **Em Análise Avançada, selecione gráficos de linhas**:<br> Perf<br> &#124; em que ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; resumir AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU do contêiner | 
| **Em Análise Avançada, selecione gráficos de linhas**:<br> Perf &#124; em que ObjectName == "Container" e CounterName == "Memory Usage MB"<br> &#124; resumir AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memória de contêiner |

## <a name="how-to-stop-monitoring-with-container-health"></a>Como interromper o monitoramento com a integridade de contêiner
Se, depois de habilitar o monitoramento do contêiner do AKS, você decidir que não quer mais monitorá-lo, você poderá escolher *recusar* usando os modelos do Azure Resource Manager fornecidos com o cmdlet do PowerShell **New-AzureRmResourceGroupDeployment** ou a CLI do Azure. Um modelo JSON Especifica a configuração para *recusar*. O outro contém valores de parâmetros que você configura para especificar a ID de recurso de cluster do AKS e o grupo de recursos em que o cluster é implantado. 

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Salve esse arquivo como **OptOutTemplate.json** em uma pasta local.
3. Cole a seguinte sintaxe JSON em seu arquivo:

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

4. Edite o valor para **aksResourceId** e **aksResourceLocation** com usando os valores do cluster do AKS, que você pode encontrar na página **Propriedades** para o cluster selecionado.

    ![Página de propriedades do contêiner](./media/monitoring-container-health/container-properties-page.png)

    Enquanto você estiver na página **Propriedades**, copie também a **ID do Recurso de Espaço de Trabalho**. Esse valor será necessário se você decidir que deseja excluir o espaço de trabalho do Log Analytics posteriormente. A exclusão do espaço de trabalho do Log Analytics não é executada como parte desse processo. 

5. Salve esse arquivo como **OptOutParam.json** em uma pasta local.
6. Você está pronto para implantar o modelo. 

    * Para usar os seguintes comandos do PowerShell na pasta que contém o modelo:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Para executar o seguinte comando com a CLI do Azure no Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Se o espaço de trabalho foi criado apenas para dar suporte ao monitoramento do cluster e não for mais necessário, você terá que excluí-lo manualmente. Se você não souber como excluir um espaço de trabalho, confira [Excluir um espaço de trabalho do Azure Log Analytics com o portal do Azure](../log-analytics/log-analytics-manage-del-workspace.md). Não se esqueça da **ID do Recurso de Espaço de Trabalho** copiada anteriormente na etapa 4; você precisará dela. 

## <a name="troubleshooting"></a>solução de problemas
Esta seção fornece informações para ajudar a solucionar problemas com a integridade do contêiner.

Se a integridade do contêiner tiver sido habilitada e configurada com êxito, mas você não conseguir exibir as informações de status ou os resultados do Log Analytics ao executar uma pesquisa de logs, poderá ajudar a diagnosticar o problema fazendo o seguinte: 

1. Verifique o status do agente executando o seguinte comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o status de implantação de solução com a versão do agente *06072018* ou posterior executando o seguinte comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o status do pod para verificar se ele está em execução executando o seguinte comando: `kubectl get pods --namespace=kube-system`

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

4. Verifique os logs de agente. Quando o agente em contêineres é implantado, ele executa uma verificação rápida executando comandos do OMI e mostra a versão do agente e do provedor. 

5. Para verificar se o agente foi integrado com êxito, execute o seguinte comando: `kubectl logs omsagent-484hw --namespace=kube-system`

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

Para exibir informações detalhadas de desempenho do aplicativo e de integridade do contêiner, veja [Pesquisar logs](../log-analytics/log-analytics-log-search.md). 
