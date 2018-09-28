---
title: Treinar modelos do TensorFlow com o Azure Machine Learning
description: Saiba como executar o treinamento distribuído e de nó único de modelos do TensorFlow com o estimador do TensorFlow
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ba43593e90b78aaa0083faf4f8162a7663c0ad47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974214"
---
# <a name="how-to-train-tensorflow-models"></a>Como treinar modelos do TensorFlow

Para treinamento de DNN (rede neural profunda) com o TensorFlow, o Azure Machine Learning oferece uma aula personalizada sobre o Estimador. O Estimador do TensorFlow do SDK do Azure (não deve ser confundido com a aula [`tf.estimator.Estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator)) permite enviar facilmente trabalhos de treinamento do TensorFlow para execuções distribuídas e de nó único na computação do Azure.

## <a name="single-node-training"></a>Treinamento de nó único
O treinamento com o Estimador do TensorFlow é semelhante a usar o [Estimador de base](how-to-train-ml-models.md). Então, primeiro, leia o artigo de instruções e verifique se entendeu os conceitos apresentados nele.
  
Para executar um trabalho do TensorFlow, instancie um objeto `TensorFlow`. Você já deve ter criado seu objeto de [destino de computação](how-to-set-up-training-targets.md#batch)`compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

Agora, especificamos os seguintes parâmetros para o construtor do TensorFlow:
* `source_directory`: o diretório local que contém todos os códigos necessários para o trabalho de treinamento. Essa pasta é copiada do computador local para o remoto
* `script_params`: um dicionário que especifica os argumentos de linha de comando para o script de treinamento `entry_script`, na forma de pares <argumento de linha de comando, valor>
* `compute_target`: a computação remota em que o script de treinamento será executado, nesse caso, um cluster da [IA do Lote](how-to-set-up-training-targets.md#batch)
* `entry_script`: o caminho do arquivo (relativo ao `source_directory`) do script de treinamento a ser executado na computação remota. Esse arquivo e quaisquer arquivos adicionais de que ele depende precisam estar localizados nesta pasta
* `conda_packages`: a lista de pacotes do Python a serem instalados por meio do conda necessário para o script de treinamento. Nesse caso, o script de treinamento usa `sklearn` para carregar os dados, então, especifique esse pacote para ser instalado.  
O construtor tem outro parâmetro chamado `pip_packages` que você pode usar para quaisquer pacotes pip necessários
* `use_gpu`: defina esse sinalizador como `True` para aproveitar a GPU para treinamento. Usa `False` como padrão.

Como você está usando o estimador do TensorFlow, o contêiner usado no treinamento incluirá por padrão o pacote do TensorFlow e as dependências relacionadas necessárias para o treinamento nas CPUs e nas GPUs.

Em seguida, envie o trabalho do TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Treinamento distribuído
O Estimador do TensorFlow também permite treinar modelos em escala em todos os clusters de CPU e GPU das VMs do Azure. O treinamento do TensorFlow pode ser executado facilmente com algumas chamadas à API. Já o Azure Machine Learning gerenciará nos bastidores toda a infraestrutura e a orquestração necessárias para realizar essas cargas de trabalho.

O Azure Machine Learning é compatível com dois métodos de treinamento distribuído no TensorFlow:
1. Treinamento distribuído baseado em MPI usando a estrutura [Horovod](https://github.com/uber/horovod)
2. [TensorFlow distribuído](https://www.tensorflow.org/deploy/distributed) nativo por meio do método de servidor de parâmetro

### <a name="horovod"></a>Horovod
O [Horovod](https://github.com/uber/horovod) é uma estrutura de software livre ring-allreduce para treinamento distribuído desenvolvida pela Uber.

Para executar o TensorFlow distribuído usando a estrutura Horovod, crie o objeto do TensorFlow da seguinte maneira:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

O código acima expõe estes novos parâmetros para o construtor do TensorFlow:
* `node_count`: o número de nós a serem usados para o trabalho de treinamento. Esse argumento define `1` como padrão
* `process_count_per_node`: o número de processos (ou "trabalhos") a serem executado em cada nó. Esse argumento define `1` como padrão
* `distributed_backend`: o back-end para iniciar o treinamento distribuído, o que o Estimador oferece por meio de MPI. Esse argumento define `None` como padrão. Se você quiser realizar treinamentos distribuídos ou paralelos (por exemplo, `node_count`>1 ou `process_count_per_node`>1 ou ambos) com o MPI (e o Horovod), defina `distributed_backend='mpi'`. A implementação de MPI usada pelo Azure Machine Learning é [MPI Aberta](https://www.open-mpi.org/).

O exemplo acima executará treinamento distribuído com dois trabalhos, um por nó.

O Horovod e suas dependências serão instalados para você, então, basta importá-lo no script de treinamento `train.py`, da seguinte forma:
```Python
import tensorflow as tf
import horovod
```

Por fim, envie o trabalho do TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Servidor de parâmetros
Também é possível executar o [TensorFlow distribuído nativo](https://www.tensorflow.org/deploy/distributed), que usa o modelo de servidor de parâmetros. Nesse método, é preciso treinar em um cluster de servidores de parâmetro e trabalhos. Os trabalhos calculam os gradientes durante o treinamento, enquanto os servidores de parâmetros agregam os gradientes.

Construa o objeto do TensorFlow:
```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Preste atenção aos seguintes parâmetros para o construtor do TensorFlow no código acima:
* `worker_count`: o número de trabalhos. Esse argumento define `1` como padrão
* `parameter_server_count`: o número de servidores de parâmetro. Esse argumento define `1` como padrão
* `distributed_backend`: o back-end a ser usado para treinamento distribuído. Esse argumento define `None` como padrão. Para fazer o treinamento distribuído por meio do servidor de parâmetros, você precisará definir `distributed_backend='ps'`

#### <a name="note-on-tfconfig"></a>Observe o `TF_CONFIG`
Você também precisa dos endereços de rede e portas do cluster do [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), para que o Azure Machine Learning defina a variável de ambiente `TF_CONFIG` para você.

A variável de ambiente `TF_CONFIG` é uma cadeia de caracteres JSON. Confira um exemplo da variável para um servidor de parâmetros:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Se você estiver usando a API [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) de alto nível do TensorFlow, ele analisará essa variável `TF_CONFIG` e criará a especificação de cluster para você. 

Se você estiver usando as APIs principais de treinamento de baixo nível do TensorFlow, será necessário analisar a variável `TF_CONFIG` e criar o `tf.train.ClusterSpec` por conta própria no código do treinamento. [Neste exemplo](https://aka.ms/aml-notebook-tf-ps), você faria isso no **script de treinamento** da seguinte maneira:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Após terminar de escrever o script de treinamento e criar o objeto do TensorFlow, será possível enviar o trabalho de treinamento:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Exemplos
Para obter um tutorial sobre o treinamento do TensorFlow de nó único, confira:
* `training/03.train-tune-deploy-tensorflow/03.train-tune-deploy-tensorflow.ipynb`

Para obter um tutorial sobre TensorFlow distribuído com Horovod, confira:
* `training/04.distributed-tensorflow-with-horovod/04.distributed-tensorflow-with-horovod.ipynb`

Para obter um tutorial sobre o TensorFlow distribuído nativo, confira:
* `training/05.distributed-tensorflow-with-parameter-server/05.distributed-tensorflow-with-parameter-server.ipynb`

Obtenha estes notebooks:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
* [Acompanhar métricas de execução durante o treinamento](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
