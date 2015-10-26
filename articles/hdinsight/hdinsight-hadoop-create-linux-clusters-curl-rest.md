<properties
   	pageTitle="Criar clusters do Hadoop, HBase ou Storm em Linux no HDInsight usando cURL e o API REST do Azure | Microsoft Azure"
   	description="Aprenda a criar clusters do Hadoop, HBase ou Storm em Linux para HDInsight usando cURL e API REST do Azure."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/14/2015"
   	ms.author="larryfr"/>

#Criar clusters baseados em Linux no HDInsight usando cURL e API REST do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-create-linux-cluster-selector.md)]

A API REST do Azure permite executar operações de gerenciamento de serviços hospedados na plataforma Azure, incluindo a criação de novos recursos, como clusters HDInsight baseados em Linux. Neste documento, você aprenderá como criar modelos do Gerenciador de Recursos do Azure para configurar um cluster HDInsight e armazenamento associado, bem como usar cURL para implantar o modelo na API REST do Azure para criar um novo cluster HDInsight.

##Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI do Azure__ A CLI do Azure é usada para criar uma entidade de serviço, que é usada para gerar tokens de autenticação para solicitações para a API REST do Azure.

    Para obter informações sobre como instalar a CLI, consulte [Instalar a CLI do Azure](xplat-cli-install.md).

- __cURL__. Esse utilitário está disponível por meio de seu sistema de gerenciamento de pacotes ou pode ser baixado em [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE]Se você estiver usando o PowerShell para executar os comandos neste documento, você deverá primeiro remover o `curl` alias criado por padrão. Esse alias usa Invoke-WebRequest, um cmdlet do PowerShell, em vez do cURL quando você usa o comando `curl` de um prompt do PowerShell e retornará erros para muitos dos comandos usados neste documento.
    > 
    > Para remover esse alias, use o seguinte no prompt do PowerShell:
    >
    > ```Remove-item alias:curl`
    >
    > Quando o alias tiver sido removido, você poderá usar a versão de cURL que você instalou no seu sistema.

##Criar um modelo

Os modelos de Gerenciamento de Recursos do Azure são documentos JSON que descrevem um __grupo de recursos__ e todos os recursos que ele contém (por exemplo, HDInsight). Essa abordagem baseada em modelo permite a definição de todos os recursos necessários ao HDInsight em um modelo, e também gerenciar alterações no grupo todo por meio de __implantações__ que aplicam mudanças ao grupo.

Normalmente, os modelos são fornecidos em duas partes: o próprio modelo e um arquivo de parâmetros que você preenche com valores específicos de sua configuração. Por exemplo, o nome do cluster, o nome do administrador e a senha. Ao usar a API REST diretamente, você deve combinar esses elementos em um arquivo. O formato deste documento JSON é:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Por exemplo, esta é uma fusão dos arquivos de modelo e parâmetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password), que cria um cluster baseado em Linux usando uma senha para proteger a conta de usuário do SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
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
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
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
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Esse exemplo será usado nas etapas neste documento. Você deve substituir os _valores_ do espaço reservado na seção __Parâmetros__ no final do documento com os valores que você deseja usar para seu cluster.

##Faça logon na sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure a partir da Interface de Linha de Comando do Azure (Azure CLI)](xplat-cli-connect.md) e conectar à sua assinatura usando o método de __logon__.

##Criar uma entidade de serviço

> [AZURE.IMPORTANT]Ao seguir as etapas no artigo vinculado a seguir, você deve fazer as seguintes alterações:
> 
> * Quando as etapas pedirem para usar um valor de __leitor__, você deve usar __proprietário__. Isso criará uma entidade de serviço que pode fazer alterações em serviços de sua assinatura, que é necessária para a criação de um cluster HDInsight.
>
> Você também deve salvar as seguintes informações usadas nesse processo:
> 
> * ID da assinatura - recebida ao usar `azure account list`
> * ID do locatário - recebida ao usar `azure account list`
> * ID do aplicativo - retornado ao criar a entidade de serviço
> * Senha para a entidade de serviço - usada ao criar o serviço principal

Siga as etapas na seção _Autenticar entidade de serviço com uma senha - CLI do Azure_ do documento [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](https://azure.microsoft.com/pt-BR/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli). Isso criará uma nova entidade de serviço que pode ser usada para autenticar a solicitação de criação de cluster.

##Obtenha um token de autenticação

Use o seguinte para obter um novo token do Azure. Substitua __TENANTID__, __APPLICATIONID__ e __PASSWORD__ pelas informações salvas durante a criação de uma entidade de serviço:

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON.

> [AZURE.IMPORTANT]O documento JSON retornado por essa solicitação conterá um elemento denominado __access\_token__; o valor desse elemento é o token de acesso que você deve usar para autenticação de solicitações usadas nas próximas seções deste documento.

##Criar um grupo de recursos

Use o seguinte para criar um novo grupo de recursos. Você deve criar o grupo primeiro e depois criar os recursos como o cluster HDInsight.

* Substitua __SUBSCRIPTIONID__ pela ID da assinatura recebida ao criar a entidade de serviço.
* Substitua __ACCESSTOKEN__ pelo token de acesso recebido na etapa anterior.
* Substitua __LOCALDODATACENTER__ pelo data center no qual você quer criar o grupo de recursos e os recursos. Por exemplo, "Centro-Sul dos EUA". 
* Substitua __GROUPNAME__ pelo nome que você deseja usar para esse grupo:

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON com informações sobre o grupo. O `"provisioningState"` elemento conterá um valor de `"Succeeded"`.

##Criar uma implantação

Use o seguinte para implantar a configuração de cluster (modelo de valores de parâmetro) no grupo de recursos.

* Substitua __SUBSCRIPTIONID__ e __ACCESSTOKEN__ pelos valores usados anteriormente. 
* Substitua __GROUPNAME__ pelo nome do grupo de recursos criado na seção anterior:
* Substitua __DEPLOYMENTNAME__ pelo nome que você deseja usar para essa implementação.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{definir a cadeia de caracteres do corpo para o modelo e os parâmetros}"

> [AZURE.NOTE]Se você salvou o documento JSON que contém o modelo e os parâmetros para um arquivo, você pode usar o seguinte, em vez de `-d "{ modelo e parâmetros}"':
>
> ```--data-binary "@/path/to/file.json"```

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON com informações sobre a operação de implantação.

> [AZURE.IMPORTANT]Observe que a implantação foi enviada, mas não foi concluída no momento. Pode levar vários minutos, normalmente em torno de 15, para concluir a implantação.

##Verificar o status de uma implantação

Para verificar o status da implantação, use o seguinte:

* Substitua __SUBSCRIPTIONID__ e __ACCESSTOKEN__ pelos valores usados anteriormente. 
* Substitua __GROUPNAME__ pelo nome do grupo de recursos criado na seção anterior:

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

Isso retornará informações de um documento JSON que contém informações sobre a operação de implantação. O elemento `"provisioningState"` conterá o status da implantação; se ele contiver um valor de `"Succeeded"`, então a implantação foi concluída com êxito. Neste ponto, o cluster deve estar disponível para uso.

##Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster.

###Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

###Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux)

###Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->