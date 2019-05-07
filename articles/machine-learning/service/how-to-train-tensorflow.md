---
title: Treinar modelos com o TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Saiba como executar o nó único e distribuída de treinamento de modelos de TensorFlow, Keras com os TensorFlow e Keras estimadores
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: c8865c851f394d73b5446ac159b5a7799c0c9ed2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192343"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Treinar modelos de TensorFlow e o Keras com o serviço Azure Machine Learning

Você pode facilmente executar trabalhos de treinamento de TensorFlow na computação do Azure usando o [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) classe estimador no SDK do Azure Machine Learning. O `TensorFlow` estimador direciona o serviço de Azure Machine Learning para executar seu trabalho em um contêiner habilitado para TensorFlow para treinamento de rede Neural profunda (DNN).

O `TensorFlow` estimador também fornece uma camada de abstração sobre a execução, o que significa que você pode configurar facilmente parametrizadas execuções em diferentes destinos de computação sem alterar seus scripts de treinamento.

## <a name="getting-started"></a>Introdução

Envio de trabalhos com o `TensorFlow` estimador é semelhante a usar a base [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py). Portanto, recomendamos que você comece lendo o [base artigo de instruções do estimador](how-to-train-ml-models.md) para entender os conceitos abrangentes pela primeira vez.

Se você quiser começar com o serviço de Azure Machine Learning [concluir o guia de início rápido](quickstart-run-cloud-notebook.md). Você terá um ambiente de trabalho carregado com todos os nossos [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml).

## <a name="single-node-training"></a>Treinamento de nó único

Para executar um trabalho de TensorFlow, instanciar uma [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) de objeto e enviá-lo como um experimento.

O código a seguir cria uma instância de um estimador de TensorFlow e o envia como um experimento. O script de treinamento `train.py` será executada usando os parâmetros de script específico. O trabalho será executado em um habilitadas para GPU [destino de computação](how-to-set-up-training-targets.md)e o scikit-Saiba será instalado como uma dependência para `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Treinamento distribuído

O [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador também dá suporte a treinamento distribuído entre os clusters de CPU e GPU. Você pode facilmente executar trabalhos do TensorFlow distribuídos e serviço Azure Machine Learning irá gerenciar a infraestrutura e a orquestração para você.

O serviço do Azure Machine Learning dá suporte a dois métodos de treinamento distribuído TensorFlow:

* [Com base em MPI](https://www.open-mpi.org/) distribuídos treinamento usando o [Horovod](https://github.com/uber/horovod) framework
* Native [distribuídos TensorFlow](https://www.tensorflow.org/deploy/distributed) usando o método de servidor de parâmetro

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) é uma estrutura de código-fonte aberto para o treinamento distribuído desenvolvido pela Uber. Ele oferece um caminho fácil para os trabalhos de GPU TensorFlow distribuídos.

O exemplo a seguir executa um trabalho de treinamento distribuído usando Horovod com dois trabalhadores distribuídos entre dois nós.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod e suas dependências serão instaladas para você, portanto, você poderá importá-lo em seu script de treinamento.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Servidor de parâmetros

Também é possível executar o [TensorFlow distribuído nativo](https://www.tensorflow.org/deploy/distributed), que usa o modelo de servidor de parâmetros. Nesse método, é preciso treinar em um cluster de servidores de parâmetro e trabalhos. Os trabalhos calculam os gradientes durante o treinamento, enquanto os servidores de parâmetros agregam os gradientes.

O exemplo a seguir executa um trabalho de treinamento distribuído usando o método de parâmetro de servidor com quatro trabalhos distribuídos entre dois nós.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Observe o `TF_CONFIG`

Você também precisará os endereços de rede e portas do cluster para o [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), portanto, o Azure Machine Learning define o `TF_CONFIG` variável de ambiente para você.

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

Para o nível de alta do TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, o TensorFlow irá analisar isso `TF_CONFIG` especificação da variável e o cluster de compilação para você.

Para nível inferior APIs centrais do TensorFlow para treinamento, analisar os `TF_CONFIG` variável e compilação os `tf.train.ClusterSpec` em seu código de treinamento. [Neste exemplo](https://aka.ms/aml-notebook-tf-ps), você faria isso no **script de treinamento** da seguinte maneira:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Suporte ao Keras

[Keras](https://keras.io/) é uma API de Python de DNN populares e de alto nível que suporta o TensorFlow, CNTK e Theano como back-ends. Se você estiver usando o TensorFlow como back-end, a adição de Keras é tão simple quanto incluindo um `pip_package` parâmetro de construtor.

O exemplo a seguir instancia um [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador e o envia como um experimento. O avaliador de executa o script de treinamento do Keras `keras_train.py`. O trabalho será executado em um habilitadas para gpu [destino de computação](how-to-set-up-training-targets.md) com o Keras instalado como uma dependência por meio de pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Exportar para ONNX

Para obter a inferência otimizada com o [ONNX Runtime](concept-onnx.md), você pode converter seu modelo TensorFlow no formato ONNX. Veja o [exemplo](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Exemplos

Você pode encontrar exemplos de código de funcionamento para execuções de TensorFlow de nó único e distribuídas usando várias estruturas em [nossa página do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Próximas etapas

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)