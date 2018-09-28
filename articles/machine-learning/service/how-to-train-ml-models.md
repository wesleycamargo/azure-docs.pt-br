---
title: Treinar modelos de aprendizado de máquina com o Aprendizado de Máquina do Azure
description: Aprenda como realizar o treinamento de nó único e distribuído de modelos tradicionais de aprendizado de máquina e aprendizagem profunda com os serviços do Azure Machine Learning
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983575"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Como treinar modelos com o Aprendizado de Máquina do Azure

Treinar modelos de aprendizado de máquina, particularmente redes neurais profundas, é muitas vezes uma tarefa de tempo e computação intensiva. Uma vez que você tenha terminado de escrever seu script de treinamento e rodar em um pequeno subconjunto de dados em sua máquina local, você provavelmente desejará ampliar sua carga de trabalho.

Para facilitar o treinamento, o SDK do Python de Aprendizado de Máquina do Azure fornece uma abstração de alto nível, a classe Estimator, que permite aos usuários treinar facilmente seus modelos no ecossistema do Azure. Você pode criar e usar um objeto Estimador para enviar qualquer código de treinamento que deseja executar na computação remota, seja uma execução de um único nó ou um treinamento distribuído em um cluster da GPU. Para tarefas PyTorch e TensorFlow, o Aprendizado de Máquina do Azure também fornece os Estimadores PyTorch e TensorFlow personalizados, que facilitam o uso dessas estruturas.

## <a name="train-with-an-estimator"></a>Treinar com um estimador

Depois de criar sua [área de trabalho](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) e configurar seu [ambiente de desenvolvimento](how-to-configure-environment.md), o treinamento de um modelo no Aprendizado de Máquina do Azure envolve as seguintes etapas:  
1. [Criar um destino de computação remota](how-to-set-up-training-targets.md)
2. [Carregar seus dados de treinamento](how-to-access-data.md) (opcional)
3. Criar seu script de treinamento
4. Criar um objeto de avaliador
5. Enviar o trabalho de treinamento

Este artigo se concentra nas etapas 4 a 5. Para as etapas 1 a 3, consultem a este [tutorial](tutorial-train-models-with-aml.md) para obter um exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

O código a seguir percorre um treinamento de nó único executado em computação remota no Azure para um scikit-aprender o modelo. Você já deve ter criado seu [destino de computação](how-to-set-up-training-targets.md#batch) objeto `compute_target` e sua [datastore](how-to-access-data.md) objeto `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

O trecho de código acima Especifica os parâmetros a seguir para o construtor de avaliador:
* `source_directory`: O diretório local que contém todo o código necessário para o trabalho de treinamento. Essa pasta é copiada em seu computador local para a computação remota 
* `script_params`: Um dicionário especificando os argumentos de linha de comando para o seu script de treinamento `entry_script`, na forma de < argumento de linha de comando, valor > pares
* `compute_target`: A computação remota que seu script de treinamento será executado, nesse caso, uma [IA do lote](how-to-set-up-training-targets.md#batch) cluster
* `entry_script`: O caminho do arquivo (relativo ao `source_directory`) do script de treinamento para ser executado na computação remota. Esse arquivo e quaisquer arquivos adicionais que ele depende devem ser localizados nesta pasta
* `conda_packages`: A lista de pacotes do Python a serem instalados via conda, necessária ao seu script de treinamento.  
O construtor tem outro parâmetro chamado `pip_packages` que você pode usar para qualquer pacote pip necessário

Agora que você criou seu objeto Estimador, é possível enviar o trabalho de treinamento para ser executado na computação remota por meio de uma chamada para a função `submit` no [experimento](concept-azure-machine-learning-architecture.md#experiment) objeto `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Pastas Especiais** Duas pastas, *produzem* e *registros*, recebem tratamento especial do Aprendizado de Máquina do Azure. Durante o treinamento, se você gravar arquivos em pastas chamadas *saídas* e *logs* que são relativas ao diretório raiz (`./outputs` e `./logs`, respectivamente), esses arquivos serão automaticamente enviados ao seu histórico de execuções para que você tenha acesso a elas assim que sua execução terminar. 
>
> Para acessar os artefatos criados durante o treinamento (como arquivos de modelo, pontos de verificação, arquivos de dados ou imagens plotadas), grave-os na pasta `./outputs`.
>
> Da mesma forma, você pode escrever todos os logs da sua corrida de treinamento para a pasta `./logs`. Para utilizar a integração [TensorBoard](https://aka.ms/aml-notebook-tb) do Azure Machine Learning, certifique-se de gravar seus registros TensorBoard nessa pasta. Enquanto a sua corrida estiver em andamento, você poderá lançar o TensorBoard e transmitir esses logs.  Mais tarde, você também poderá restaurar os logs de qualquer uma das execuções anteriores.
>
> Por exemplo, para fazer o download de um arquivo gravado na pasta *de saídas* para sua máquina local após a execução do seu treinamento remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Treinamento distribuído e imagens personalizadas do Docker

Existem dois cenários de treinamento adicionais que você pode realizar com o Estimador:
1. Usando uma imagem do Docker personalizada
2. Treinamento distribuído em um cluster com vários nó

O código a seguir mostra como realizar o treinamento distribuído para um modelo CNTK. Além disso, em vez de usar as imagens padrão do Azure Machine Learning, você pressupõe que você tenha sua própria imagem de encaixe personalizada que deseja usar para treinamento.

Você já deve ter criado seu [destino de computação](how-to-set-up-training-targets.md#batch) objeto `compute_target`. Você pode criar o avaliador da seguinte maneira:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

O código acima expõe os seguintes parâmetros novos para o construtor de avaliador:
* `custom_docker_base_image`: O nome da imagem que você deseja usar. Você só pode fornecer imagens disponíveis em repositórios do docker público (no Hub do Docker neste caso). Para usar uma imagem de um repositório privado do docker, use o construtor `environment_definition` parâmetro em vez disso
* `node_count`: o número de nós a serem usados no seu trabalho de treinamento. Esse argumento assume como padrão `1`
* `process_count_per_node`: O número de processos (ou "funcionários") para ser executado em cada nó. Nesse caso, você usa o `2` GPUs disponíveis em cada nó. Esse argumento assume como padrão `1`
* `distributed_backend`: O back-end para iniciar distribuído treinamento, o que o avaliador de oferece por meio de MPI. Esse argumento padrão é `None`. Se você quer realizar treinamento paralelo ou distribuído (por exemplo, `node_count`> 1 ou `process_count_per_node`> 1 ou os dois), defina `distributed_backend='mpi'`. A implementação do MPI usada pela AML é [Open MPI](https://www.open-mpi.org/).

Por fim, envie o trabalho de treinamento:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Exemplos
Para obter um tutorial sobre treinar um modelo sklearn, consulte:
* `tutorials/01.train-models.ipynb`

Para obter um tutorial sobre CNTK distribuído usando o docker personalizado, consulte:
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

Obtenha esses blocos de anotações:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Treinar modelos de PyTorch](how-to-train-pytorch.md)
* [Treinar modelos de TensorFlow](how-to-train-tensorflow.md)
* [Ajustar os hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
