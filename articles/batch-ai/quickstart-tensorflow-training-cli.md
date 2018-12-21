---
title: Início Rápido do Azure – treinamento de aprendizado profundo – CLI do Azure | Microsoft Docs
description: Início Rápido – aprenda rapidamente a treinar uma rede neural de aprendizado profundo do TensorFlow em uma única GPU com IA do Lote usando a CLI do Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408061"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Início Rápido: Treinar um modelo de aprendizado profundo com a IA do Lote

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Este início rápido mostra como treinar um modelo de aprendizado profundo de exemplo em uma máquina virtual habilitada para GPU gerenciada pela IA do Lote. A IA do Lote é um serviço gerenciado para que cientistas de dados e pesquisadores de IA possam treinar modelos de IA e de aprendizado de máquina em larga escala em clusters de máquinas virtuais do Azure. 

Neste exemplo, você deve usar a CLI do Azure a fim de configurar a IA do Lote para treinar uma rede neural [TensorFlow](https://www.tensorflow.org/) de exemplo no [banco de dados MNIST](http://yann.lecun.com/exdb/mnist/) de dígitos manuscritos. Depois de concluir este início rápido, você entenderá os conceitos principais do uso da IA do Lote para treinar um modelo de IA ou de aprendizado de máquina e estará pronto para tentar treinar modelos diferentes em uma escala maior.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Este início rápido pressupõe que você está executando comandos em um shell Bash, seja no Cloud Shell ou no computador local. Se você já concluiu o início rápido para [criar um cluster da IA do Lote com a CLI do Azure](quickstart-create-cluster-cli.md), ignore as duas primeiras etapas para criar um grupo de recursos e um cluster da IA do Lote.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus2*. Escolha a localização Leste dos EUA 2 ou outro local em que o serviço IA do Lote esteja disponível. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Criar um cluster do Batch AI

Primeiro, use o comando `az batchai workspace create` para criar um *espaço de trabalho* da IA do Lote. Você precisa de um espaço de trabalho para organizar os clusters da IA do Lote e outros recursos.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Para criar um cluster da IA do Lote, use o comando `az batchai cluster create`. O exemplo abaixo cria um cluster de um nó com as seguintes propriedades:

* Usa o tamanho de VM NC6, que tem uma GPU NVIDIA Tesla K80. O Azure oferece vários tamanhos de VM com diferentes GPUs NVIDIA.
* Executa uma imagem do Ubuntu Server padrão criada para hospedar aplicativos baseados em contêiner. Você pode executar a maioria das cargas de trabalho de treinamento nessa distribuição. 
* Adiciona uma conta de usuário chamada *myusername*e gera chaves SSH se elas não ainda existirem no local de chave padrão (*~/.ssh*) de seu ambiente local.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

A saída do comando mostra as propriedades do cluster. Demora alguns minutos para criar e iniciar o nó. Para ver o status do cluster, execute o comando `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

No início na criação do cluster, a saída é semelhante ao seguinte, mostrando que o cluster está `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Continue nas etapas a seguir para carregar o script de treinamento e criar o trabalho de treinamento enquanto o cluster sofre as alterações de estado. O cluster estará pronto para executar o trabalho de treinamento quando o estado for `steady` e o nó único estiver `Idle`.

## <a name="upload-training-script"></a>Carregar script de treinamento

Use o comando `az storage account create` para criar uma conta de armazenamento a fim de armazenar o script de treinamento e a saída de treinamento.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Crie um compartilhamento de arquivos do Azure chamado `myshare` na conta usando o comando `az storage share create`:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Use o comando `az storage directory create` para criar diretórios no compartilhamento de arquivos do Azure. Crie o diretório `scripts` para o script de treinamento e `logs` para a saída do treinamento:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

No shell Bash, crie um diretório de trabalho local e baixe o exemplo [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) do TensorFlow. O script em Python treina uma rede neural convolucional no conjunto de imagens MNIST de 60 mil dígitos manuscritos de 0 a 9. Em seguida, ele testa o modelo em um conjunto de exemplos de teste.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Faça upload do script para o diretório `scripts` no compartilhamento usando o comando `az storage file upload`.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Enviar trabalho de treinamento

Primeiro, crie um *experimento* da IA do Lote no espaço de trabalho usando o comando `az batchai experiment create`. Um experimento é um contêiner lógico para trabalhos relacionados da IA do Lote.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Em seu diretório de trabalho, crie um arquivo de configuração de trabalho de treinamento `job.json` com o conteúdo a seguir. Passe esse arquivo de configuração ao enviar o trabalho de treinamento.

Esse arquivo `job.json` inclui configurações para localizar o arquivo de script do Python e executá-lo em um contêiner do TensorFlow no nó de GPU. Ele também especifica onde salvar os arquivos de saída do trabalho no armazenamento do Azure. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` indica que o nome da conta de armazenamento será especificado durante o envio do trabalho.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Use o comando `az batchai job create` para enviar o trabalho no nó passando o arquivo de configuração `job.json` e o nome da conta de armazenamento:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

O comando retorna as propriedades do trabalho e, em seguida, leva alguns minutos para ser concluído. Para monitorar o progresso desse trabalho, use o comando `az batchai job file stream` para transmitir o arquivo `stdout-wk-0.txt` do diretório de saída padrão no nó. O script de treinamento gera esse arquivo depois que o trabalho começa a ser executado.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Saída de exemplo:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

O streaming é interrompido quando o trabalho é concluído. O script de exemplo treina por mais de 10 *épocas* ou passa pelo conjunto de dados de treinamento. Neste exemplo, depois de 10 épocas, o modelo treinado tem desempenho com erro de teste de apenas 0,8%.

## <a name="get-job-output"></a>Obter saída do trabalho

A IA do Lote cria uma estrutura de pastas exclusiva na conta de armazenamento para a saída de cada trabalho. Defina a variável de ambiente JOB_OUTPUT_PATH com esse caminho. Em seguida, liste os arquivos de saída no armazenamento usando o comando `az storage file list`:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

A saída é semelhante a:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Use o comando `az storage file download` para baixar um ou mais arquivos em seu diretório de trabalho local. Por exemplo: 

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser continuar com exemplos e tutoriais da IA do Lote, use o espaço de trabalho da IA do Lote, o cluster e a conta de armazenamento criados neste início rápido. 

Você será cobrado pelo cluster da IA do Lote enquanto os nós estão em execução. Se você quiser manter a configuração do cluster quando não tiver nenhum trabalho a ser executado, redimensione o número de nós do cluster para zero. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Depois, redimensione para um ou mais nós a fim de executar os trabalhos. Quando você não precisar mais de um cluster, exclua-o com o comando `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Quando não for mais necessário, você poderá usar o comando `az group delete` para remover o grupo de recursos da IA do Lote e os recursos de armazenamento. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você aprendeu a usar a IA do Lote para treinar um modelo de aprendizado profundo TensorFlow de exemplo em uma VM com GPU única usando a CLI do Azure. Para saber mais sobre como distribuir o treinamento do modelo em um cluster de GPU maior, prossiga o tutorial da IA do Lote.

> [!div class="nextstepaction"]
> [Tutorial de treinamento distribuído](./tutorial-horovod-tensorflow.md)