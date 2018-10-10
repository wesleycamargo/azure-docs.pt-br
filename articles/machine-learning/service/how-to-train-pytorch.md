---
title: Treinar modelos PyTorch com o Azure Machine Learning
description: Saiba como executar o treinamento distribuído e de nó único de modelos PyTorch com o estimador do PyTorch
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977019"
---
# <a name="how-to-train-pytorch-models"></a>Como treinar modelos de PyTorch

Para treinamento de DNN (rede neural profunda) com o PyTorch, o Azure Machine Learning oferece uma aula PyTorch personalizada sobre o Estimador. O Estimador do PyTorch do SDK do Azure permite enviar facilmente trabalhos de treinamento do PyTorch para execuções distribuídas e de nó único na computação do Azure.

## <a name="single-node-training"></a>Treinamento de nó único
O treinamento com o Estimador do PyTorch é semelhante a usar o [Estimador de base](how-to-train-ml-models.md). Então, primeiro, leia o artigo de instruções e verifique se entendeu os conceitos apresentados nele.
  
Para executar um trabalho do PyTorch, instancie um objeto `PyTorch`. Você já deve ter criado seu [destino de computação](how-to-set-up-training-targets.md#batch) objeto `compute_target` e sua [datastore](how-to-access-data.md) objeto `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Agora, especificamos os seguintes parâmetros para o construtor do PyTorch:
* `source_directory`: O diretório local que contém todo o código necessário para o trabalho de treinamento. Essa pasta é copiada em seu computador local para a computação remota
* `script_params`: Um dicionário especificando os argumentos de linha de comando para o seu script de treinamento `entry_script`, na forma de < argumento de linha de comando, valor > pares
* `compute_target`: A computação remota que seu script de treinamento será executado, nesse caso, uma [IA do Lote](how-to-set-up-training-targets.md#batch) cluster
* `entry_script`: O caminho do arquivo (relativo ao `source_directory`) do script de treinamento para ser executado na computação remota. Esse arquivo e quaisquer arquivos adicionais que ele depende devem ser localizados nesta pasta
* `conda_packages`: A lista de pacotes do Python a serem instalados via conda, necessária ao seu script de treinamento.
O construtor tem outro parâmetro chamado `pip_packages` que você pode usar para qualquer pacote pip necessário
* `use_gpu`: defina esse sinalizador como `True` para aproveitar a GPU para treinamento. Usa `False` como padrão

Como você está usando o estimador do PyTorch, o contêiner usado no treinamento incluirá por padrão o pacote do PyTorch e as dependências relacionadas necessárias para o treinamento nas CPUs e nas GPUs.

Em seguida, envie o trabalho do PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Treinamento distribuído
O Estimador do PyTorch também permite treinar modelos em escala em todos os clusters de CPU e GPU das VMs do Azure. O treinamento do PyTorch pode ser executado facilmente com algumas chamadas à API. Já o Azure Machine Learning gerenciará nos bastidores toda a infraestrutura e a orquestração necessárias para realizar essas cargas de trabalho.

O Azure Machine Learning atualmente dá suporte ao treinamento distribuído baseado em MPI do PyTorch usando a estrutura Horovod.

### <a name="horovod"></a>Horovod
O [Horovod](https://github.com/uber/horovod) é uma estrutura de software livre ring-allreduce para treinamento distribuído desenvolvida pela Uber.

Para executar o PyTorch distribuído usando a estrutura Horovod, crie o objeto do PyTorch da seguinte maneira:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

O código acima expõe os seguintes parâmetros novos para o construtor do PyTorch:
* `node_count`: o número de nós a serem usados no seu trabalho de treinamento. Esse argumento assume como padrão `1`
* `process_count_per_node`: o número de processos (ou "trabalhos") a serem executado em cada nó. Esse argumento assume como padrão `1`
* `distributed_backend`: O back-end para iniciar distribuído treinamento, o que o avaliador de oferece por meio de MPI. Esse argumento define `None` como padrão. Se você quiser realizar treinamentos distribuídos ou paralelos (por exemplo, `node_count`>1 ou `process_count_per_node`>1 ou ambos) com o MPI (e o Horovod), defina `distributed_backend='mpi'`. A implementação de MPI usada pelo Azure Machine Learning é [MPI Aberta](https://www.open-mpi.org/).

O exemplo acima executará treinamento distribuído com dois trabalhos, um por nó.

O Horovod e suas dependências serão instalados para você, então, basta importá-lo no script de treinamento `train.py`, da seguinte forma:
```Python
import torch
import horovod
```

Por fim, envie seu trabalho do PyTorch distribuído:
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Exemplos
Para obter um tutorial sobre o treinamento do PyTorch de nó único, confira:
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Para obter um tutorial sobre o PyTorch distribuído com Horovod, confira:
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

Obtenha esses blocos de anotações:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
