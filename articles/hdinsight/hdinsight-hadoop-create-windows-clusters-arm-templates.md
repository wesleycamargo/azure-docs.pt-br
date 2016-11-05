---
title: Criar clusters Hadoop baseados em Windows no HDInsight usando modelos do Azure Resource Manager | Microsoft Docs
description: Aprenda a criar clusters do Azure HDInsight usando modelos do Azure Resource Manager.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jgao

---
# Criar clusters Hadoop baseados em Windows no HDInsight usando modelos do Azure Resource Manager
[!INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a criar clusters do HDInsight usando modelos do Azure Resource Manager. Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../resource-group-template-deploy.md). Para outros recursos e ferramentas de criação de cluster, clique na guia Selecionar na parte superior dessa página ou consulte [Métodos de criação de cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

## Pré-requisitos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

* [Assinatura do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* CLI do Azure ou Azure PowerShell
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## Modelos do Gerenciador de Recursos
O modelo do Resource Manager simplifica a criação de clusters do HDInsight, seus recursos dependentes (como a conta de armazenamento padrão) e outros recursos (como o Banco de Dados SQL do Azure para usar o Apache Sqoop) para seu aplicativo em uma única operação coordenada. No modelo, você define os recursos que são necessários para o aplicativo e especifica os parâmetros de implantação para inserir valores para ambientes diferentes. O modelo consiste em JSON e expressões que podem ser usados na construção de valores para sua implantação.

Um modelo do Resource Manager para a criação de um cluster do HDInsight e a conta dependente do Armazenamento do Azure podem ser encontrados no [Apêndice A](#appx-a-arm-template). Use um editor de texto para salvar o modelo em um arquivo na sua estação de trabalho. Você aprenderá a chamar o modelo usando várias ferramentas.

Para obter mais informações sobre o modelo do Resource Manager, consulte

* [Criar modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md)
* [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../resource-group-template-deploy.md)

## Implantação com o PowerShell
O procedimento a seguir cria um cluster do HDInsight.

**Para implantar um cluster usando o modelo do Resource Manager**

1. Salve o arquivo JSON encontrado no [Apêndice A](#appx-a-arm-template) em sua estação de trabalho.
2. Defina os parâmetros, se necessário.
3. Execute o modelo usando o seguinte script do PowerShell:
   
        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion
   
        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
   
        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName
   
        $location = "East US 2"
   
        $armDeploymentName = $namePrefix
        #endregion
   
        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion
   
        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location
   
        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}
   
        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters
   
        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 
   
    O script do PowerShell configura apenas o nome do cluster e o nome da conta de armazenamento. Você pode definir outros valores do modelo de ARM.

Para obter mais informações, veja [Implantar com o PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Implantar com a CLI do Azure
O exemplo a seguir cria um cluster e sua conta de armazenamento dependente e o contêiner chamando um modelo de ARM:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{"clusterName":{"value":"hdi1229win"},"clusterStorageAccountName":{"value":"hdi1229store"},"location":{"value":"East US 2"},"clusterLoginPassword":{"value":"Pass@word1"}}"





## Implantar com a API REST
Veja [Implantar com a API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Implantação com o Visual Studio
Com o Visual Studio você pode criar um projeto do grupo de recursos e implantá-lo ao Azure por meio da interface do usuário. Selecione o tipo de recursos a serem incluídos em seu projeto e os recursos serão adicionados automaticamente ao modelo do Gerenciador de recursos. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, veja [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](../virtual-machines/virtual-machines-windows-csharp-template.md).
* Para obter um exemplo detalhado de implantação de um aplicativo, confira [Provisionar e implantar microsserviços de forma previsível no Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
* Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](../solution-dev-test-environments.md).
* Para saber mais sobre as seções do modelo do Azure Resource Manager, veja [Criando modelos](../resource-group-authoring-templates.md).
* Para obter uma lista das funções que podem ser usadas em um modelo do Azure Resource Manager, veja [Funções do modelo](../resource-group-template-functions.md).

## Appx-A: modelo do Resource Manager
O modelo a seguir do Gerenciador de Recursos do Azure cria um cluster Hadoop baseado em Windows com a conta de armazenamento do Azure dependente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

<!---HONumber=AcomDC_0914_2016-->