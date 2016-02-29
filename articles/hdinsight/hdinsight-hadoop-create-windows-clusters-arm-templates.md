<properties
   pageTitle="Criar clusters Hadoop baseados no Windows no HDInsight usando modelos de ARM | Microsoft Azure"
   	description="Aprenda a criar clusters do Azure HDInsight usando modelos de ARM do Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/02/2016"
   ms.author="jgao"/>

# Criar clusters Hadoop baseados no Windows no HDInsight usando modelos ARM

[AZURE.INCLUDE [seletor](../../includes/hdinsight-create-windows-cluster-selector.md)]

Aprenda a criar clusters HDInsight usando modelos de ARM (Gerenciador de Recursos do Azure). Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../resource-group-template-deploy.md). Para outros recursos e ferramentas de criação de cluster, clique na guia Selecionar na parte superior dessa página ou consulte [Métodos de criação de cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

###Pré-requisitos:

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- [Assinatura do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater) e/ou [CLI do Azure](../xplat-cli-install.md). 

## Modelos de ARM

O modelo de ARM simplifica a criação de clusters HDInsight, seus recursos dependentes (como a conta de armazenamento padrão) e outros recursos (como o Banco de Dados SQL do Azure para usar o Apache Sqoop) para seu aplicativo em uma única operação coordenada. No modelo, você define os recursos que são necessários para o aplicativo e especifica os parâmetros de implantação para inserir valores para ambientes diferentes. O modelo consiste em JSON e expressões que podem ser usados na construção de valores para sua implantação.

Um modelo de ARM para criar um cluster HDInsight e a conta de Armazenamento do Azure dependente podem ser encontrados no [Apêndice A](). Use um editor de texto para salvar o modelo em um arquivo na sua estação de trabalho. Você aprenderá a chamar o modelo usando várias ferramentas.

Para obter mais informações sobre o modelo de ARM, consulte

- [Criar modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md)
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../resource-group-template-deploy.md)


## Implantação com o PowerShell

O procedimento a seguir cria o cluster HDInsight baseados em Linux.

**Para implantar um cluster usando o modelo do ARM**

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
	
Para saber mais, confira [Implantar com PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Implantar com a CLI do Azure

O exemplo a seguir cria um cluster e sua conta de armazenamento dependente e o contêiner chamando um modelo de ARM:

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-windows-template.json" -p "{"clusterName":{"value":"hdi1229win"},"clusterStorageAccountName":{"value":"hdi1229store"},"location":{"value":"East US 2"},"clusterLoginPassword":{"value":"Pass@word1"}}"

## Implantar com a API REST

Consulte [Implantar com a API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Implantação com o Visual Studio

Com o Visual Studio você pode criar um projeto do grupo de recursos e implantá-lo ao Azure por meio da interface do usuário. Selecione o tipo de recursos a serem incluídos em seu projeto e os recursos serão adicionados automaticamente ao modelo do Gerenciador de recursos. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, confira [Criação e implantação de grupos de recursos do Azure usando o Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

- Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, confira [Implantar recursos usando bibliotecas .NET e um modelo](../virtual-machines/arm-template-deployment.md).
- Para obter um exemplo detalhado de implantação de um aplicativo, confira [Provisionar e implantar microsserviços de forma previsível no Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](../solution-dev-test-environments.md).
- Para saber mais sobre as seções do modelo do Gerenciador de Recursos do Azure, confira [Criação de modelos](../resource-group-authoring-templates.md).
- Para obter uma lista das funções que você pode usar em um modelo do Gerenciador de Recursos do Azure, confira [Funções do modelo](../resource-group-template-functions.md).


##Apêndice A: modelo de ARM

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



##Apêndice B: modelo de ARM: cluster baseado em Linux

O modelo a seguir do Gerenciador de Recursos do Azure cria um cluster Hadoop baseado em Linux com a conta de armazenamento do Azure dependente.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
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
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "username",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
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
	        "osType": "Linux",
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
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
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

<!---HONumber=AcomDC_0218_2016-->