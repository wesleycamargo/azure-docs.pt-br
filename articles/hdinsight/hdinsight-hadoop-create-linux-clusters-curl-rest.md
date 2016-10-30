<properties
    pageTitle="Criar clusters do Hadoop, HBase ou Storm em Linux no HDInsight usando cURL e o API REST do Azure | Microsoft Azure"
    description="Saiba como criar clusters HDInsight baseados em Linux usando cURL, modelos do Gerenciador de Recursos do Azure e a API REST do Azure. Você pode especificar o tipo de cluster (Hadoop, HBase ou Storm) ou usar scripts para instalar componentes personalizados."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>


#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Criar clusters baseados em Linux no HDInsight usando cURL e API REST do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

A API REST do Azure permite executar operações de gerenciamento de serviços hospedados na plataforma Azure, incluindo a criação de novos recursos, como clusters HDInsight baseados em Linux. Neste documento, você aprenderá como criar modelos do Gerenciador de Recursos do Azure para configurar um cluster HDInsight e armazenamento associado, bem como usar cURL para implantar o modelo na API REST do Azure para criar um novo cluster HDInsight.

> [AZURE.IMPORTANT] As etapas neste documento usam o número padrão de nós de trabalho (4) para um cluster HDInsight. Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI do Azure__. A CLI do Azure é usada para criar uma entidade de serviço, que é usada para gerar tokens de autenticação para solicitações para a API REST do Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Este utilitário está disponível por meio do sistema de gerenciamento de pacotes ou pode ser baixado em [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Se você estiver usando o PowerShell para executar comandos neste documento, deverá primeiro remover o alias `curl` criado por padrão. Esse alias usa Invoke-WebRequest, um cmdlet do PowerShell, em vez do cURL quando você usa o comando `curl` de um prompt do PowerShell e retornará erros para muitos dos comandos usados neste documento.
    > 
    > Para remover esse alias, use o seguinte no prompt do PowerShell:
    >
    > `Remove-item alias:curl`
    >
    > Quando o alias tiver sido removido, você poderá usar a versão de cURL que você instalou no seu sistema.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Criar um modelo

Os modelos de Gerenciamento de Recursos do Azure são documentos JSON que descrevem um __grupo de recursos__ e todos os recursos que ele contém (por exemplo, HDInsight). Essa abordagem baseada em modelo permite que você defina todos os recursos necessários ao HDInsight em um modelo, e gerencie alterações no grupo todo por meio de __implantações__ que aplicam alterações ao grupo.

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

Por exemplo, esta é uma fusão dos arquivos de modelo e parâmetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que cria um cluster baseado em Linux usando uma senha para proteger a conta de usuário do SSH.

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

Esse exemplo será usado nas etapas neste documento. Você deve substituir os _valores_ do espaço reservado na seção __Parâmetros__ no final do documento pelos valores que quer usar no cluster.

##<a name="login-to-your-azure-subscription"></a>Faça logon em sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure a partir da Interface de Linha de Comando do Azure (Azure CLI)](../xplat-cli-connect.md) e conectar à sua assinatura usando o comando `azure login`.

##<a name="create-a-service-principal"></a>Criar uma entidade de serviço

> [AZURE.NOTE] Essas etapas são uma versão resumida das informações fornecidas na seção _Autenticar a entidade de serviço com uma senha - CLI do Azure_ do documento [Autenticar uma entidade de serviço com o Azure Resource Manager](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) . Estas etapas criam uma nova entidade de serviço que pode ser usada para autenticar as solicitações da API REST usadas para criar recursos do Azure, como um cluster HDInsight.

1. No prompt de comando, em uma sessão de terminal ou no shell, use o comando a seguir para listar as assinaturas do Azure.

        azure account list
        
    Na lista, selecione a assinatura que você deseja usar e observe a coluna __ID__ . Essa é a __ID da assinatura__ e será usada na maioria das etapas neste documento.

2. Criar um novo aplicativo no Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Substitua os valores de `--name`, `--home-page` e `--identifier-uris` pelos seus próprios valores. Forneça uma senha para a nova entrada do Active Directory.
    
    > [AZURE.NOTE] Como você está criando esse aplicativo para autenticação por meio de uma entidade de serviço, os valores `--home-page` e `--identifier-uris` não precisam fazer referência a uma página da Web hospedada na Internet; precisam apenas ser URIs únicos.
    
    Dos dados retornados, salve o valor __AppId__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Crie uma entidade de serviço usando o valor __AppId__ retornado anteriormente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Dos dados retornados, salve o valor __Object Id__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Atribua a função __Proprietário__ à entidade de serviço usando o valor __ID do Objeto__ retornado anteriormente. Você também deve usar o valor __ID da assinatura__ obtido anteriormente.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Quando esse comando for concluído, a entidade de serviço agora terá acesso de Proprietário para a ID de assinatura especificada.

##<a name="get-an-authentication-token"></a>Obtenha um token de autenticação

1. Use o item a seguir para localizar o valor __ID do Locatário__ para sua assinatura.

        azure account show -s <subscription ID>
        
    Nos dados retornados, localize o valor __ID do Locatário__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Gere um novo token usando a API REST do Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Substitua __TenantID__, __AppID__ e __senha__ pelos valores obtidos ou usados anteriormente.

    Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON.

    O documento JSON retornado por essa solicitação conterá um elemento denominado __access_token__; o valor desse elemento é o token de acesso que você deve usar para autenticação de solicitações usadas nas próximas seções deste documento.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Criar um grupo de recursos

Use o seguinte para criar um novo grupo de recursos. Você deve criar o grupo primeiro e depois criar os recursos como o cluster HDInsight. 

* Substitua __SubscriptionID__ pela ID da assinatura recebida ao criar a entidade de serviço.
* Substitua __AccessToken__ pelo token de acesso recebido na etapa anterior.
* Substitua __DataCenterLocation__ pelo data center no qual você quer criar o grupo de recursos e os recursos. Por exemplo, "Centro-Sul dos EUA". 
* Substitua __ResourceGroupName__ pelo nome que deseja usar para esse grupo.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON com informações sobre o grupo. O elemento `"provisioningState"` conterá um valor de `"Succeeded"`.

##<a name="create-a-deployment"></a>Criar uma implantação

Use o seguinte para implantar a configuração de cluster (modelo de valores de parâmetro) no grupo de recursos.

* Substitua __SubscriptionID__ e __AccessToken__ pelos valores usados anteriormente. 
* Substitua __ResourceGroupName__ pelo nome do grupo de recursos criado na seção anterior.
* Substitua __DeploymentName__ pelo nome que você deseja usar para essa implementação.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Se você salvou o documento JSON que contém o modelo e os parâmetros para um arquivo, você pode usar o seguinte, em vez de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series, e o corpo da resposta conterá um documento JSON com informações sobre a operação de implantação.

> [AZURE.IMPORTANT] Observe que a implantação foi enviada, mas não foi concluída no momento. Pode levar vários minutos, normalmente em torno de 15, para concluir a implantação.

##<a name="check-the-status-of-a-deployment"></a>Verificar o status de uma implantação

Para verificar o status da implantação, use o seguinte:

* Substitua __SubscriptionID__ e __AccessToken__ pelos valores usados anteriormente. 
* Substitua __ResourceGroupName__ pelo nome do grupo de recursos criado na seção anterior.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Isso retornará informações de um documento JSON que contém informações sobre a operação de implantação. O elemento `"provisioningState"` conterá o status da implantação; se ele contiver um valor de `"Succeeded"`, então a implantação foi concluída com êxito. Neste ponto, o cluster deve estar disponível para uso.

##<a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster. 

###<a name="hadoop-clusters"></a>Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)



<!--HONumber=Oct16_HO2-->


