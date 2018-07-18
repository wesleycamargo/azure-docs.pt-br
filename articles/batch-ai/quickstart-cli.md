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
ms.openlocfilehash: 3601ea412790c991892a0c05210d2551810287b8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869012"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Execute um trabalho de treinamento do CNTK usando a CLI do Azure

A CLI 2.0 do Azure permite criar e gerenciar recursos da IA do Lote: criar/excluir servidores de arquivos e clusters da IA do Lote e enviar/terminar/excluir/monitorar trabalhos de treinamento.

Este início rápido mostra como criar um cluster de GPU e executar um trabalho de treinamento usando o Microsoft Cognitive Toolkit.

O script de treinamento [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) está disponível na página da IA do Lote no GitHub. Esse script treina a rede neural convolucional no banco de dados MNIST de dígitos manuscritos.

O exemplo de CNTK oficial foi modificado para aceitar o local do conjunto de dados de treinamento e o local do diretório de saída por meio de argumentos de linha de comando.

## <a name="quickstart-overview"></a>Visão geral do Início Rápido

* Criar um cluster de GPU de nó único (com tamanho de VM `Standard_NC6`) chamado `nc6`;
* Criar uma nova conta de armazenamento para armazenar os trabalhos de entrada e saída;
* Criar um Compartilhamento de Arquivos do Azure com duas pastas, `logs` e `scripts`, para armazenar a saída de trabalhos e os scripts de treinamento;
* Criar um Contêiner de Blobs do Azure `data` para armazenar dados de treinamento;
* Implantar o script de treinamento e os dados de treinamento no compartilhamento de arquivos e no contêiner criados;
* Configure o trabalho para montar o Compartilhamento de Arquivos do Azure e o Contêiner de Blobs do Azure no nó do cluster e disponibilizá-los como sistema de arquivos regular em `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` e `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` é uma variável de ambiente definida pela IA do Lote para o trabalho.
* Monitorar a execução do trabalho fazendo streaming de sua saída padrão;
* Após a conclusão do trabalho, inspecionar sua saída e os modelos gerados;
* No final, excluir todos os recursos alocados.

# <a name="prerequisites"></a>pré-requisitos

* Assinatura do Azure - caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Acesso à CLI 2.0 do Azure com a versão 2.0.31 ou superior. Você pode usar a CLI 2.0 do Azure disponível no [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) ou instalá-la localmente seguindo [estas instruções](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

# <a name="cloud-shell-only"></a>Apenas o Cloud Shell

Se você estiver usando o Cloud Shell, altere o diretório de trabalho para `/usr/$USER/clouddrive`, pois seu diretório base não tem nenhum espaço vazio:

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico para implantar e gerenciar recursos do Azure. O comando a seguir cria um novo grupo de recursos ```batchai.quickstart``` no local Leste dos EUA:

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>Criar o cluster GPU

O comando a seguir cria um cluster de GPU de nó único (o tamanho da VM é Standard_NC6) usando Ubuntu DSVM como a imagem do sistema operacional:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

O Ubuntu DSVM permite que você execute todos os trabalhos de treinamento em contêineres do Docker e execute as estruturas de aprendizado profundo mais populares diretamente na VM.

A opção `--generate-ssh-keys` manda a CLI do Azure gerar chaves SSH públicas e privadas, caso você ainda não as tenha. Você pode acessar os nós de cluster usando o nome do usuário e a chave SSH gerada atuais.

Se você usar o Cloud Shell, faça backup da pasta ~/.ssh em algum armazenamento permanente.

Saída de exemplo:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O comando a seguir cria uma nova conta de armazenamento na mesma região do grupo de recursos batchai.repices. Atualize o comando com um nome de conta de armazenamento exclusivo.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Se o nome da conta de armazenamento selecionada não estiver disponível, o comando acima relatará o erro correspondente. Nesse caso, escolha outro nome e tente novamente.

# <a name="data-deployment"></a>Implantação de dados

## <a name="download-the-training-script-and-training-data"></a>Baixar o script de treinamento e os dados de treinamento

* Baixe e extraia o Banco de Dados MNIST pré-processado [deste local](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) para a pasta atual.

Para GNU/Linux ou Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Observe que talvez seja necessário instalar `unzip` se sua distribuição GNU/Linux não o tiver.

* Baixe o script de exemplo [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) para a pasta atual:

Para GNU/Linux ou Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Criar compartilhamento de arquivos do Azure e implantar o script de treinamento

Os comandos a seguir criam os Compartilhamentos de Arquivos do Azure `scripts` e `logs` e copiam o script de treinamento na pasta `cntk` do compartilhamento `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Criar um contêiner de blobs e implantar dados de treinamento

Os comandos a seguir criam o Contêiner de Blobs do Azure `data` e copiam os dados de treinamento na pasta `mnist_cntk`:
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Enviar trabalho de treinamento

## <a name="prepare-job-configuration-file"></a>Preparar arquivo de configuração de trabalho

Crie um arquivo de configuração de trabalho de treinamento `job.json` com o seguinte conteúdo:
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Esse arquivo de configuração especifica:

* `nodeCount` – número de nós exigidos pelo trabalho (1 para este início rápido);
* `cntkSettings` – especifica o caminho do script de treinamento e os argumentos de linha de comando. Os argumentos de linha de comando incluem o caminho para os dados de treinamento e o caminho de destino para armazenar os modelos gerados. `AZ_BATCHAI_OUTPUT_MODEL` é uma variável de ambiente definida pela IA do Lote com base na configuração do diretório de saída (confira abaixo);
* `stdOutErrPathPrefix` – caminho em que a IA do Lote criará diretórios contendo a saída e os logs do trabalho;
* `outputDirectories` – conjunto de diretórios de saída a ser criado pela IA do Lote. Para cada diretório, a IA do Lote cria uma variável de ambiente com o nome `AZ_BATCHAI_OUTPUT_<id>`, em que `<id>` é o identificador do diretório;
* `mountVolumes` – lista de sistemas de arquivos a ser montado durante a execução do trabalho. Os sistemas de arquivos são montados em `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` é uma variável de ambiente definida pela IA do Lote;
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` indica que o nome da conta de armazenamento será especificado durante o envio do trabalho; seja pelo parâmetro storage-account-name ou pela variável de ambiente `AZURE_BATCHAI_STORAGE_ACCOUNT` em seu computador.

## <a name="submit-the-job"></a>Enviar o trabalho

Use o comando a seguir para enviar o trabalho no cluster:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Saída de exemplo:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>Monitorar a execução do trabalho

O script de treinamento está relatando o progresso do treinamento no arquivo `stderr.txt` dentro do diretório de saída padrão. Você pode monitorar o progresso usando o seguinte comando:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Saída de exemplo:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

O streaming é interrompido quando o trabalho é concluído (com êxito ou falha).

# <a name="inspect-generated-model-files"></a>Inspecionar arquivos de modelo gerados

O trabalho armazena os arquivos de modelo gerados no diretório de saída com o atributo `id` igual a `MODEL`. Você pode listar os arquivos de modelo e obter URLs de download usando o comando a seguir:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

Saída de exemplo:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Como alternativa, você pode usar o Portal ou o Gerenciador de Armazenamento do Azure para inspecionar os arquivos gerados. Para distinguir a saída de trabalhos diferentes, a IA do Lote cria uma estrutura de pasta exclusiva para cada um deles. Você pode encontrar o caminho para a pasta que contém a saída usando o atributo `jobOutputDirectoryPathSegment` do trabalho enviado:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Saída de exemplo:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Excluir recursos

Exclua o grupo de recursos e todos os recursos alocados com o seguinte comando:

```azurecli
az batchai group delete -n batchai.quickstart -y
```
