---
title: Início Rápido do Azure – Criar cluster da IA do Lote – CLI do Azure | Microsoft Docs
description: Início Rápido – Criar um cluster da IA do Lote para treinar modelos de aprendizado de máquina e de IA – CLI do Azure
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
ms.openlocfilehash: 1ea12c9a544704ea91b85ae944e611e6769b5592
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407126"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Início Rápido: Criar um cluster para trabalhos de treinamento da IA do Lote usando a CLI do Azure

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Este início rápido mostra como usar a CLI do Azure para criar um cluster da IA do Lote que você pode usar para treinar modelos de IA e de aprendizado de máquina. A IA do Lote é um serviço gerenciado para que cientistas de dados e pesquisadores de IA possam treinar modelos de aprendizado de máquina e de IA em larga escala em clusters de máquinas virtuais do Azure.

Inicialmente, o cluster tem um único nó de GPU. Depois de concluir este início rápido, você terá um cluster que poderá ser escalado verticalmente e usado para treinar seus modelos. Envie trabalhos de treinamento para o cluster usando a IA do Lote, as ferramentas do [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou as [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Este início rápido pressupõe que você está executando comandos em um shell Bash, seja no Cloud Shell ou no computador local.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus2*. Escolha uma localização na qual o serviço IA do Lote esteja disponível, por exemplo, Leste dos EUA 2.

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

Para criar um cluster da IA do Lote, use o comando `az batchai cluster create`. O exemplo abaixo cria um cluster com as seguintes propriedades:

* Contém um único nó em uma VM NC6, que tem uma GPU NVIDIA Tesla K80. 
* Executa uma imagem do Ubuntu Server padrão, criada para hospedar aplicativos baseados em contêiner e que você pode usar na maioria das cargas de trabalho de treinamento. 
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

Na fase inicial de criação do cluster, a saída é semelhante ao seguinte, mostrando o cluster no estado `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
O cluster está pronto para ser usado quando o estado é `steady` e o único nó está `Idle`.

## <a name="list-cluster-nodes"></a>Listar nós de cluster 

Se você precisa se conectar aos nós do cluster (nesse caso, um único nó) para instalar aplicativos ou realizar a manutenção, obtenha as informações de conexão executando o comando `az batchai cluster node list`:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

A saída JSON deverá ser semelhante a:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Use essas informações a fim de criar uma conexão SSH para o nó. Por exemplo, insira o endereço IP correto do seu nó no seguinte comando:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Saia da sessão SSH para continuar.

## <a name="resize-the-cluster"></a>Redimensionar o cluster

Quando você usa o cluster para executar um trabalho de treinamento, pode ser necessário usar mais recursos de computação. Por exemplo, para aumentar o tamanho de um trabalho de treinamento distribuído para dois nós, execute o comando [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize):

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

O redimensionamento do cluster demora alguns minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser continuar com exemplos e tutoriais da IA do Lote, use o espaço de trabalho da IA do Lote criado neste início rápido. 

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
    --resource-group myResourceGroup \
```

Quando não for mais necessário, você poderá usar o comando `az group delete` para remover o grupo de recursos dos recursos da IA do Lote. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster da IA do Lote usando a CLI do Azure. Para saber mais sobre como usar um cluster da IA do Lote para treinar um modelo, prossiga para o início rápido Treinar um modelo de aprendizado profundo.

> [!div class="nextstepaction"]
> [Treinar um modelo de aprendizado profundo](./quickstart-tensorflow-training-cli.md)
