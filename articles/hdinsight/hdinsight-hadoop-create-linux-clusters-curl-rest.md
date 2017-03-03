---
title: Criar o Azure HDInsight (Hadoop) usando cURL e REST | Microsoft Docs
description: "Saiba como criar clusters HDInsight baseados em Linux usando cURL, modelos do Azure Resource Manager e a API REST do Azure. Você pode especificar o tipo de cluster (Hadoop, HBase ou Storm) ou usar scripts para instalar componentes personalizados."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: c8ed0760650f7fb3c85eef4ec7f72c60bb0efd00
ms.lasthandoff: 02/21/2017


---
# <a name="create-hdinsight-clusters-using-curl-and-the-azure-rest-api"></a>Criar clusters HDInsight usando cURL e API REST do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como criar um cluster HDInsight usando um modelo do Azure Resource Manager e a API REST do Azure.

A API REST do Azure permite executar operações de gerenciamento de serviços hospedados na plataforma Azure, incluindo a criação de novos recursos, como clusters HDInsight.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **CLI do Azure 2.0** (visualização). A CLI do Azure é usada para criar uma entidade de serviço, que é usada para gerar tokens de autenticação para solicitações para a API REST do Azure. Para obter mais informações sobre a visualização da CLI do Azure 2.0, consulte [Introdução à CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

* **cURL**. Este utilitário está disponível por meio do sistema de gerenciamento de pacotes ou pode ser baixado em [http://curl.haxx.se/](http://curl.haxx.se/).

  > [!NOTE]
  > Se você estiver usando o PowerShell para executar comandos neste documento, deverá primeiro remover o alias `curl` criado por padrão. Este alias usa Invoke-WebRequest em vez de cURL. Se não remover este alias, você poderá receber erros com alguns dos comandos usados neste documento.
  >
  > Para remover esse alias, use o seguinte comando no prompt do PowerShell:
  >
  > `Remove-item alias:curl`
  >
  > Quando o alias tiver sido removido, você poderá usar a versão de cURL que você instalou no seu sistema.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>Criar um modelo

Os modelos do Azure Resource Manager são documentos JSON que descrevem um **grupo de recursos** e todos os recursos que ele contém (por exemplo, HDInsight). Essa abordagem baseada em modelo permite que você defina os recursos necessários para o HDInsight em um modelo.

O exemplo a seguir é uma fusão dos arquivos de modelo e parâmetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que cria um cluster baseado em Linux usando uma senha para proteger a conta de usuário do SSH.

   ```json
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
                           "description": "The location where all azure resources are deployed."
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
                       "clusterVersion": "3.5",
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
   ```

Esse exemplo será usado nas etapas presentes neste documento. Substitua os *valores* de exemplo na seção **Parâmetros** pelos valores para o cluster.

> [!IMPORTANT]
> O modelo usa o número padrão de nós de trabalho (4) para um cluster HDInsight. Se você planeja ter mais de 32 nós de trabalho, será necessário selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Entre na sua assinatura do Azure

Siga as etapas documentadas em [Introdução à CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) e conecte-se à sua assinatura usando o comando `az login`.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

> [!NOTE]
> Essas etapas são uma versão resumida da seção *Criar a entidade de serviço com uma senha* do documento [Usar a CLI do Azure para criar uma entidade de serviço para acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password). Estas etapas criam uma entidade de serviço que é usada para autenticar a API REST do Azure.

1. Em uma linha de comando, use o seguinte comando para listar as assinaturas do Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na lista, selecione a assinatura que você deseja usar e observe as colunas **Subscription_ID** e __Tenant_ID__. Salve esses valores.

2. Use o comando a seguir para criar um aplicativo no Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Substitua os valores de `--display-name`, `--homepage` e `--identifier-uris` pelos seus próprios valores. Forneça uma senha para a nova entrada do Active Directory.

   > [!NOTE]
   > Os valores `--home-page` e `--identifier-uris` não precisam fazer referência a uma página da Web real hospedada na Internet. Eles devem ser URIs exclusivos.

   O valor retornado deste comando é a __ID do aplicativo__ do novo aplicativo. Salve esse valor.

3. Use o comando a seguir para criar uma entidade de serviço usando a **ID do aplicativo**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     O valor retornado deste comando é a __ID de Objeto__. Salve esse valor.

4. Atribua a função **Proprietário** à entidade de serviço usando o valor da **ID de Objeto**. Use o valor **ID da assinatura** obtido anteriormente.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Obtenha um token de autenticação

Use o seguinte comando para recuperar um token de autenticação:

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you receive a 200 series response and the response body contains a JSON document.

    The JSON document returned by this request contains an element named **access_token**. The value of **access_token** is used to authentication requests to the REST API.

   ```json
   {
       "token_type":"Bearer",
       "expires_in":"3599",
       "expires_on":"1463409994",
       "not_before":"1463406094",
       "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
   }
   ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Use o que é descrito a seguir para criar um grupo de recursos.

* Substitua **SubscriptionID** pela ID da assinatura recebida ao criar a entidade de serviço.
* Substitua **AccessToken** pelo token de acesso recebido na etapa anterior.
* Substitua **DataCenterLocation** pelo data center no qual você quer criar o grupo de recursos e os recursos. Por exemplo, "Centro-Sul dos EUA".
* Substitua **ResourceGroupName** pelo nome que deseja usar para esse grupo.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series e o corpo da resposta conterá um documento JSON com informações sobre o grupo. O elemento `"provisioningState"` contém um valor de `"Succeeded"`.

## <a name="create-a-deployment"></a>Criar uma implantação

Execute o comando a seguir para implantar o modelo no grupo de recursos.

* Substitua **SubscriptionID** e **AccessToken** pelos valores usados anteriormente.
* Substitua **ResourceGroupName** pelo nome do grupo de recursos criado na seção anterior.
* Substitua **DeploymentName** pelo nome que você deseja usar para essa implementação.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Se você salvou o modelo em um arquivo, você pode usar o seguinte comando em vez de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se essa solicitação for bem-sucedida, você receberá uma resposta do 200 series e o corpo da resposta conterá um documento JSON com informações sobre a operação de implantação.

> [!IMPORTANT]
> Até agora, a implantação foi enviada mas ainda não concluída. Pode levar vários minutos, normalmente em torno de 15, para concluir a implantação.

## <a name="check-the-status-of-a-deployment"></a>Verificar o status de uma implantação

Para verificar o status da implantação, use o comando a seguir:

* Substitua **SubscriptionID** e **AccessToken** pelos valores usados anteriormente.
* Substitua **ResourceGroupName** pelo nome do grupo de recursos criado na seção anterior.

```bash
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Esse comando retorna informações de um documento JSON que contém informações sobre a operação de implantação. O elemento `"provisioningState"` contém o status da implantação. Se ele contém um valor de `"Succeeded"`, implantação foi concluída com êxito.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster.

### <a name="hadoop-clusters"></a>Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

