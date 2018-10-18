---
title: Trabalhar com clusters de IA do Lote do Azure | Microsoft Docs
description: Como escolher uma configuração de cluster de IA do Lote e criar e gerenciar um cluster de IA
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61294d8b6b84b03b1e0c8d79b4d2855452c7f0e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056632"
---
# <a name="work-with-batch-ai-clusters"></a>Trabalhar com clusters de IA do Lote 

Este artigo explica como trabalhar com clusters de IA do Lote do Azure. Ele apresenta o conceito de clusters, os tipos de configurações possíveis e exemplos. A maioria dos exemplos para criar e gerenciar um cluster neste artigo usa a CLI do Azure. No entanto, você pode usar outras ferramentas, como o portal do Azure e os SDKs de IA do Lote do Azure para trabalhar com clusters.

Um cluster de IA do Lote é um dos vários recursos no serviço. Confira a [visão geral dos recursos de IA do Lote](resource-concepts.md) para entender o escopo de clusters no serviço.

## <a name="introduction-to-clusters"></a>Introdução aos clusters

Um cluster de IA do Lote contém os recursos de computação para executar trabalhos de treinamento de IA e de aprendizado de máquina. Todos os nós em um cluster têm o mesmo tamanho de VM e a mesma imagem do sistema operacional. A IA do Lote oferece várias opções para a criação de clusters que são personalizados para diferentes necessidades. Normalmente, você configura um cluster diferente para cada categoria de poder de processamento necessária para concluir um projeto. Você pode escalar e reduzir verticalmente o número de nós em um cluster com base na demanda e no orçamento. Os clusters podem ser provisionados e compartilhados em uma equipe ou cada pessoa pode provisionar seu próprio cluster. 

Cada cluster existe em um recurso de IA do Lote chamado de *workspace*. Antes de provisionar um cluster, você precisa ter um workspace de IA do Lote configurado. Por exemplo, se você usar a CLI do Azure, use o comando [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) para configurar um workspace. 

Depois de criar um cluster, você pode enviar um trabalho de cada vez para uma fila. Em seguida, a IA do Lote manipula o processo de alocação de recurso para os trabalhos em execução no cluster. 

## <a name="cluster-configuration-options"></a>Opções de configuração de cluster

Ao planejar um cluster, primeiro determine os requisitos de computação. A IA do Lote oferece opções de configuração flexíveis para que você possa personalizar um cluster de acordo com suas necessidades. A seguir estão as principais opções a serem consideradas ao provisionar um cluster:

* **Tamanho da VM** – escolha um [tamanho de VM](../virtual-machines/linux/sizes.md) que esteja disponível em uma [região com suporte](https://azure.microsoft.com/global-infrastructure/services/) para nós de cluster. Cada cluster só pode conter um tamanho de VM, portanto, se as tarefas exigem vários tipos de VMs, você precisa provisionar um cluster separado para cada tipo de requisito de computação. Para treinar modelos de IA ou de aprendizado de máquina desenvolvidos com estruturas que aproveitam GPUs, confira os [tamanhos de VM otimizados para GPU](../virtual-machines/linux/sizes-gpu.md) no Azure.
  
* **Prioridade VM** – a IA do Lote oferece VMs de baixa prioridade ou dedicadas para um cluster. As VMs dedicadas são reservadas para serem usadas no cluster. A opção de baixa prioridade aloca a capacidade não usada da VM do Azure com uma economia de custo significativa em troca da possibilidade de que os trabalhos admitam preempção caso o Azure recupere as VMs. Os trabalhos executados por mais de 24 horas em uma VM de baixa prioridade também admitam preempção automaticamente. Se houver preocupação com o orçamento, considere o uso de VMs de baixa prioridade para trabalhos de experimentação curtos. E quando chegar a hora de executar trabalhos mais longos, mude para VMs dedicadas.

* **Número de nós** – a IA do Lote oferece opções manuais e de dimensionamento automático do número de nós no cluster. Com a opção manual, você controla quando escalar ou reduzir um cluster verticalmente, para que você possa gerenciar seus próprios custos de computação. A opção de dimensionamento automático garante que o cluster sempre seja reduzido verticalmente quando não esteja sendo usado, minimizando os custos de computação. 

  Se você optar por dimensionar o cluster manualmente, defina a meta inicial durante a criação do cluster. A meta é o número de nós que a IA do Lote aloca inicialmente. Mais tarde, você pode redimensionar manualmente o número de nós.  

  Se você escolher a opção de dimensionamento automático, defina o número mínimo e máximo de nós da meta durante a criação do cluster. A meta pode variar de 0 até o número máximo de nós com suporte da [Cota de núcleos da IA do Lote](quota-limits.md). A meta 0 permite que você mantenha a configuração do cluster sem ser cobrado por nenhum custo de computação. Se você solicitar uma meta maior do que o limite de cota com suporte, o provisionamento falhará. 

* **Imagem de VM** – por padrão, a IA do Lote provisiona as VMs do cluster com uma imagem do Ubuntu Server padrão que dá suporte a cargas de trabalho de contêiner. Você pode escolher outra imagem pré-configurada do Linux no Azure Marketplace, como uma [Máquina Virtual de Ciência de Dados](../machine-learning/data-science-virtual-machine/overview.md). 

* **Armazenamento** – a IA do Lote fornece uma opção de armazenamento automático, que você pode escolher ao criar um cluster usando a CLI do Azure. Essa opção cria automaticamente um contêiner de Blobs e de Compartilhamento de arquivos do Azure em uma nova conta de armazenamento. Esses recursos de armazenamento são montados para cada um dos nós no cluster durante o tempo de execução, permitindo que os arquivos sejam acessados de um caminho local. Os nomes de conta de armazenamento, o nome do compartilhamento de arquivo, o nome do contêiner de Blobs e os caminhos de montagem têm valores padrão, que também podem ser personalizados usando parâmetros adicionais durante a criação do cluster. 

  Se não for definida nenhuma opção de armazenamento, você precisará criar os recursos de armazenamento separadamente e defini-los ao enviar trabalhos. Essa opção será útil se o cluster for gerenciado no nível da organização, mas o armazenamento for gerenciado no nível do usuário. Para obter mais informações sobre como fazer upload de arquivos no Armazenamento do Azure e acessá-los durante o tempo de execução, confira [Store Batch AI job input and output with Azure Storage](use-azure-storage.md) (Armazenar entrada e saída de trabalhos da IA do Lote com o Armazenamento do Azure).

* **Acesso de usuário** – a IA do Lote permite que você gere arquivos de chaves SSH pública e privada ao criar um cluster, ou forneça suas próprias chaves SSH para entrar com SSH em nós individuais. Você também pode definir um nome de usuário (por padrão, definido como o usuário atual) e uma senha. Essas credenciais podem ser usadas para acessar os nós durante a execução para exibir várias métricas ou obter mais insights sobre os trabalhos.

* **Tarefa de configuração** – a IA do Lote permite que você defina os argumentos de linha de comando a serem executados em cada nó após a alocação. Você também pode definir o caminho em que o arquivo de saída deve ser registrado. Use essa opção quando houver etapas de provisionamento adicionais além da imagem base.

* **Configuração adicional** – há vários cenários menos comuns que podem exigir configurações mais especializadas. Nesse caso, um [arquivo de configuração de cluster](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) pode ser anexado ao comando de CLI do Azure para criar um cluster. 

## <a name="create-the-cluster"></a>Criar o cluster

Depois de decidir sobre as opções de configuração do cluster, use a CLI do Azure, o portal do Azure ou APIs de IA do Lote para criar o cluster. Por exemplo, para criar um cluster usando a CLI do Azure, você pode seguir a documentação de [az batchai cluster create](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) para criar o comando exato que oferece as configurações que você precisa. 

Na configuração mais básica, o comando a seguir provisiona um cluster Standard_NC6 com um nó e acesso SSH. Por padrão, esse cluster contém VMs dedicadas que executam a imagem padrão mais recente do Ubuntu Server.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

O exemplo a seguir provisiona um cluster Standard_NC6 que pode ser dimensionado automaticamente de 0 a 4 nós e usa nós de baixa prioridade e uma conta de armazenamento automático. Essa é uma boa configuração se você precisa de um cluster de baixo custo e fácil de gerenciar. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

O exemplo a seguir provisiona um cluster Standard_NC6 que inclui uma imagem de Máquina Virtual de Ciência de Dados, opções personalizadas de armazenamento e montagem, credenciais de SSH personalizadas, uma tarefa de instalação que instala o pacote *unzip* e um arquivo de configuração de cluster com uma configuração adicional. Essa configuração é um exemplo de um cluster mais personalizado de acordo com suas necessidades.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>Monitorar o cluster

Os comandos [az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) e [az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) podem ser usados para monitorar várias informações para cada um dos clusters.

### <a name="list-all-clusters"></a>Listar todos os clusters

O seguinte comando lista a todos os clusters em um workspace.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>Mostrar informações sobre um cluster

O comando a seguir mostra as informações completas sobre um cluster específico no formato de tabela.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Se o cluster for provisionado usando a opção de armazenamento automática, você poderá recuperar o nome da conta de armazenamento a ser usada ao carregar trabalhos de treinamento e scripts. Use o seguinte comando:

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

A saída deve ser semelhante à seguinte.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Listar nós de cluster

Se você precisa se conectar a nós do cluster, o comando a seguir recupera a lista de nós e as informações de conexão.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

A saída de cada nó será semelhante à seguinte:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
Use essas informações para fazer uma conexão SSH a um nó usando um comando semelhante ao seguinte.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Enviar trabalhos ao cluster

Após o provisionamento do cluster, você poderá enviar trabalhos a serem executados nos nós. Confira o comando [az batchai job](/cli/azure/batchai/job?view=azure-cli-latest) para obter diferentes maneiras de preparar, enviar e monitorar trabalhos usando a CLI do Azure. 

## <a name="downscale-cluster-for-later-use"></a>Reduzir a escala do cluster para uso posterior

Depois de concluir os trabalhos em execução, você poderá reduzir a escala do cluster. É recomendado sempre reduzir a escala dos clusters quando eles não estão sendo usados para economizar custos de computação. A redução de escala de um cluster para 0 nós permite interromper os encargos de cobrança sem a necessidade de provisionar novamente os clusters no futuro, quando precisar aumentar a escala. Se o dimensionamento automático for selecionado na criação do cluster, a escala do cluster será diminuída automaticamente para a meta mínima. Se o dimensionamento manual for selecionado, reduza a escala do cluster usando o comando a seguir.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Excluir um cluster

Depois que você terminar de usar um cluster, use o comando [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) para excluí-lo.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

A exclusão do grupo de recursos também exclui automaticamente todos os clusters que foram provisionados em um grupo de recursos.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de criação de um cluster de IA do Lote, confira o Início Rápido do [Portal](quickstart-create-cluster-portal.md) ou a [CLI do Azure](quickstart-create-cluster-cli.md) ou as [receitas do IA do Lote](https://github.com/Azure/BatchAI/tree/master/recipes) no GitHub.
