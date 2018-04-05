---
title: Início Rápido do Azure - Treinamento do CNTK com IA do Lote - CLI do Azure | Microsoft Docs
description: Aprenda rapidamente a executar um trabalho de treinamento do CNTK com IA do Lote usando a CLI do Azure
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Execute um trabalho de treinamento do CNTK usando a CLI do Azure

Este início rápido fornece detalhes usando a interface de linha de comando (CLI) do Azure para executar um trabalho de treinamento do Microsoft Cognitive Toolkit (CNTK) usando o serviço do IA do Lote. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts.

Neste exemplo, você pode usar o banco de dados MNIST de imagens manuscritas para treinar uma rede neural convolucional (CNN) em um cluster GPU de nó único pela AI do Lote. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido exige que você esteja executando a última versão da CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

Os provedores de recursos do IA do Lote também precisam ser registrados uma vez para a sua assinatura usando o Azure Cloud Shell ou a CLI do Azure. Um registro do provedor pode levar até 15 minutos.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os trabalhos e os clusters da IA do Lote são recursos do Azure e devem ser colocados em um grupo de recursos do Azure.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create).

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*. Ele usa o comando [az configure](/cli/azure/reference-index#az_configure) para definir esse grupo de recursos e o local como padrão.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Definir os valores padrão para o comando `az` é uma etapa opcional. Você pode optar por não definir os valores padrão. Se optar por definir os padrões, você deve remover as configurações padrão depois de concluir o tutorial. Remova as configurações padrão usando os comandos a seguir:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Este início rápido usa uma conta de armazenamento do Azure para hospedar dados e scripts para o trabalho de treinamento. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Cada conta de armazenamento deve ter um nome exclusivo. No comando `az` anterior e outros comandos semelhantes neste tutorial, substitua o valor para a configuração `mystorageaccount` com o nome da sua conta de armazenamento.

## <a name="prepare-azure-file-share"></a>Preparar o compartilhamento de arquivos do Azure

Para fins de ilustração, este início rápido usa um compartilhamento de arquivos do Azure para hospedar os dados de treinamento e scripts para o trabalho de aprendizado.

1. Crie um compartilhamento de arquivos chamado *batchaiquickstart* usando o comando [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. Crie um diretório no nome do compartilhamento *mnistcntksample* usando o comando [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Baixe o [pacote de exemplo](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) e descompacte. Carregue o conteúdo para o diretório usando o comando [az storage file upload](/cli/azure/storage/file#az_storage_file_upload):

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Criar o cluster GPU
Use o comando [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) para criar um cluster de IA do Lote que consiste em um único nó de VM de GPU. Neste exemplo, a VM executa a imagem padrão Ubuntu LTS. Especifique `image UbuntuDSVM` para executar a Máquina Virtual de Aprendizado Profundo, que oferece suporte a estruturas de treinamento adicional. O tamanho NC6 tem uma GPU NVIDIA K80. Monte o compartilhamento de arquivos em uma pasta chamada *azurefileshare*. O caminho completo da pasta no nó de computação GPU é $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


Depois que o registro é criado, o resultado é semelhante ao seguinte:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Obter o status do cluster

Para obter uma visão geral do status do cluster, execute o comando [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list):

```azurecli
az batchai cluster list -o table
```

A saída deverá ser semelhante a esta:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Para obter mais detalhes, execute o comando [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show). Ele retorna todas as propriedades do cluster mostradas após a criação do cluster.

O cluster está pronto quando os nós são alocados e após concluir a preparação (confira o atributo `nodeStateCounts`). Se algo der errado, o atributo `errors` contém a descrição do erro.

## <a name="create-training-job"></a>Criar um trabalho de treinamento

Depois que o cluster estiver pronto, configure e envie o trabalho de aprendizado.

1. Crie um arquivo de modelo JSON para a criação de um trabalho chamado job.json:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Crie um trabalho denominado *myjob* para executar no cluster com o comando [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create):

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

A saída deverá ser semelhante a esta:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Monitorar o trabalho

Use o comando [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) para obter uma visão geral do status do trabalho:

```azurecli
az batchai job list -o table
```

A saída deverá ser semelhante a esta:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Para obter mais detalhes, execute o comando [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

O `executionState` contém o estado atual da execução do trabalho:

* `queued`: o trabalho está aguardando que os nós de cluster fiquem disponíveis
* `running`: o trabalho está em execução
*   `succeeded` (ou `failed`): o trabalho está concluído e `executionInfo` contém os detalhes sobre o resultado


## <a name="list-stdout-and-stderr-output"></a>Saída stderr e stdout de lista
Use o comando [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) para listar os links para os arquivos de log stdout e stderr:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

A saída deverá ser semelhante a esta:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Observe a saída

Você pode transmitir ou finalizar os arquivos de saída do trabalho enquanto o trabalho está em execução. O exemplo a seguir usa o comando [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) para transmitir o log stderr.txt:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

A saída deverá ser semelhante à seguinte. Interrompa a saída, pressionando [Ctrl]-[C].

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Excluir recursos

Use o comando [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) para excluir o trabalho:

```azurecli
az batchai job delete --name myjob
```
Use o comando [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) para excluir o cluster:

```azurecli
az batchai cluster delete --name mycluster
```

Use o comando `az group delete` para excluir o grupo de recursos que você criou para este início rápido:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Restaurar as configurações padrão da CLI 2.0 do Azure

Remova as configurações padrão configuradas anteriormente para o local e o grupo de recursos:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a executar um trabalho de treinamento do CNTK em um cluster da AI do Lote, usando a CLI do Azure. Para saber mais sobre como usar a IA do Lote com kits de ferramentas diferentes, confira as [receitas treinamento](https://github.com/Azure/BatchAI).

Para saber mais sobre como usar a CLI 2.0 do Azure para gerenciar a IA do Lote, confira a [documentação do github](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
