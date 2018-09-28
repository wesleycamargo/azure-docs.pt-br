---
title: Como interromper o monitoramento do cluster do Serviço de Kubernetes do Azure | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento do seu cluster do Azure AKS com o Azure Monitor para contêineres.
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
ms.date: 09/13/2018
ms.author: magoedte
ms.openlocfilehash: 2b989fbebe237e4e3746ef2f237193587173dfe4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963399"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-azure-monitor-for-containers"></a>Como interromper o monitoramento do Azure Monitor do Serviço de Kubernetes do Azure (AKS) para contêineres

Se, depois de habilitar o monitoramento do cluster do AKS, você decidir que não quer mais monitorá-lo, você poderá escolher *recusar* usando os modelos do Azure Resource Manager fornecidos com o cmdlet do PowerShell **New-AzureRmResourceGroupDeployment** ou a CLI do Azure. Um modelo JSON Especifica a configuração para *recusar*. O outro contém valores de parâmetros que você configura para especificar a ID de recurso de cluster do AKS e o grupo de recursos em que o cluster é implantado. 

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="create-template"></a>Criar modelo

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

## <a name="remove-the-solution-using-azure-cli"></a>Remover a solução usando a CLI do Azure
Execute o comando a seguir com a CLI do Azure no Linux para remover a solução e limpar a configuração no cluster do AKS.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```azurecli
ProvisioningState       : Succeeded
```

## <a name="remove-the-solution-using-powershell"></a>Remover a solução usando o PowerShell

Execute os seguintes comandos do PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do cluster do AKS.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```powershell
ProvisioningState       : Succeeded
```

Se o espaço de trabalho foi criado apenas para dar suporte ao monitoramento do cluster e não for mais necessário, você terá que excluí-lo manualmente. Se você não souber como excluir um espaço de trabalho, confira [Excluir um espaço de trabalho do Azure Log Analytics com o portal do Azure](../log-analytics/log-analytics-manage-del-workspace.md). Não se esqueça da **ID do Recurso de Espaço de Trabalho** copiada anteriormente na etapa 4; você precisará dela. 

