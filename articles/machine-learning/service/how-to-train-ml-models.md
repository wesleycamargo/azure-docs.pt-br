---
title: Treinar modelos de aprendizado de máquina usando uma classe EstimaTOR com o Azure Machine Learning
description: Saiba como realizar o treinamento de nó único e distribuído de máquina tradicional de aprendizado e modelos usando a classe de estimador do serviço do Machine Learning de aprendizado profundo
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: c47761c184d0e6c091ff49b3eca2fdf89574b49d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114852"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Como treinar modelos com o Aprendizado de Máquina do Azure

Treinar modelos de aprendizado de máquina, particularmente redes neurais profundas, é muitas vezes uma tarefa de tempo e computação intensiva. Uma vez que você tenha terminado de escrever seu script de treinamento e rodar em um pequeno subconjunto de dados em sua máquina local, você provavelmente desejará ampliar sua carga de trabalho.

Para facilitar o treinamento, o SDK do Python do Machine Learning do Azure fornece uma abstração de alto nível, a classe Estimator, que permite aos usuários treinar facilmente seus modelos no ecossistema do Azure. Você pode criar e usar um `Estimator` objeto para enviar qualquer código de treinamento que deseja executar na computação remota, seja uma execução de um único nó ou um treinamento distribuído em um cluster da GPU. Para trabalhos de PyTorch e TensorFlow, o Azure Machine Learning também fornece personalizados respectivos `PyTorch` e `TensorFlow` estimadores para simplificar o uso dessas estruturas.

## <a name="train-with-an-estimator"></a>Treinar com um estimador

Depois de criar seu [Workspace](concept-azure-machine-learning-architecture.md#workspace) e configurar seu [ambiente de desenvolvimento](how-to-configure-environment.md), o treinamento de um modelo no Machine Learning do Azure envolve as seguintes etapas:  
1. Criar um [destino de computação remota](how-to-set-up-training-targets.md)
2. Carregar seus [dados de treinamento](how-to-access-data.md) (opcional)
3. Criar seu [script de treinamento](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um `Estimator` objeto
5. Enviar o trabalho de treinamento

Este artigo se concentra nas etapas 4 a 5. Para as etapas 1 a 3, consulte o [tutorial treinar um modelo](tutorial-train-models-with-aml.md) para obter um exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Use um `Estimator` para um treinamento de nó único executado em computação remota no Azure para um modelo scikit-learn. Você já deve ter criado seu [destino de computação](how-to-set-up-training-targets.md#batch) objeto `compute_target` e sua [datastore](how-to-access-data.md) objeto `ds`.

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

Este trecho de código especifica os seguintes parâmetros para o `Estimator` construtor.

Parâmetro | DESCRIÇÃO
--|--
`source_directory`| O diretório local que contém todo o código necessário para o trabalho de treinamento. Essa pasta é copiada em seu computador local para a computação remota 
`script_params`| Especificando os argumentos de linha de comando para o seu script de treinamento de dicionário `entry_script`, na forma de < argumento de linha de comando, valor > pares
`compute_target`| Computação remota que seu script de treinamento será executado, nesse caso, uma [IA do Lote do Azure](how-to-set-up-training-targets.md#batch) cluster
`entry_script`| FilePath (relativo ao `source_directory`) do script de treinamento para ser executado na computação remota. Esse arquivo e quaisquer arquivos adicionais que ele depende devem ser localizados nesta pasta
`conda_packages`| A lista de pacotes do Python a serem instalados via conda, necessária ao seu script de treinamento.  
O construtor tem outro parâmetro chamado `pip_packages` que você usa para qualquer pacote pip necessário

Agora que você criou seu `Estimator` de objeto, envie o trabalho de treinamento para ser executado na computação remota com uma chamada para a `submit` função no seu objeto [Experimento](concept-azure-machine-learning-architecture.md#experiment) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Pastas Especiais** Duas pastas, *produzem* e *registros*, recebem tratamento especial do Aprendizado de Máquina do Azure. Durante o treinamento, ao gravar arquivos em pastas denominadas *gera* e *logs* que são relativos ao diretório raiz (`./outputs` e `./logs`, respectivamente), os arquivos serão automaticamente Carregue o histórico de execução para que você tenha acesso a eles quando sua execução for concluída.
>
> Para criar artefatos durante o treinamento (como arquivos de modelo, os pontos de verificação, arquivos de dados ou imagens plotadas) escreva para a pasta `./outputs`.
>
> Da mesma forma, você escreve todos os logs da sua corrida de treinamento para a pasta `./logs`. Para utilizar a integração [TensorBoard](https://aka.ms/aml-notebook-tb) do Azure Machine Learning, certifique-se de gravar seus registros TensorBoard nessa pasta. Enquanto a sua corrida estiver em andamento, você poderá lançar o TensorBoard e transmitir esses logs.  Mais tarde, você também poderá restaurar os logs de qualquer uma das execuções anteriores.
>
> Por exemplo, para fazer o download de um arquivo gravado na pasta *de saídas* para sua máquina local após a execução do seu treinamento remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Treinamento distribuído e imagens personalizadas do Docker

Há dois cenários de treinamento adicional, você pode realizar com o `Estimator`:
* Usando uma imagem do Docker personalizada
* Treinamento distribuído em um cluster com vários nó

O código a seguir mostra como realizar o treinamento distribuído para um modelo CNTK. Além disso, em vez de usar as imagens do Azure Machine Learning padrão, ele pressupõe que você esteja usando sua própria imagem personalizada do docker para treinamento.

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

O código acima expõe os seguintes novos parâmetros para o `Estimator` construtor:

Parâmetro | DESCRIÇÃO | Padrão
--|--|--
`custom_docker_base_image`| O nome da imagem que você deseja usar. Fornece somente as imagens disponíveis em repositórios do docker público (no Hub do Docker neste caso). Para usar uma imagem de um repositório privado do docker, use o construtor `environment_definition` parâmetro em vez disso | `None`
`node_count`| O número de nós a serem usados no seu trabalho de treinamento. | `1`
`process_count_per_node`| Número de processos (ou "trabalhos") a serem executado em cada nó. Nesse caso, você usa o `2` GPUs disponíveis em cada nó.| `1`
`distributed_backend`| O back-end para iniciar distribuído treinamento, o que o avaliador de oferece por meio de MPI.  Para realizar treinamento paralelo ou distribuído (por exemplo, `node_count`> 1 ou `process_count_per_node`> 1 ou os dois), defina `distributed_backend='mpi'`. A implementação do MPI usada pela AML é [Open MPI](https://www.open-mpi.org/).| `None`

Por fim, envie o trabalho de treinamento:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Exemplos
Para obter um tutorial sobre treinar um modelo sklearn, consulte:
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Para obter um tutorial sobre CNTK distribuído usando o docker personalizado, consulte:
* [training/06.distributed-cntk-with-custom-docker](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/06.distributed-cntk-with-custom-docker)

Obtenha esses blocos de anotações:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Treinar modelos de PyTorch](how-to-train-pytorch.md)
* [Treinar modelos de TensorFlow](how-to-train-tensorflow.md)
* [Ajustar os hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
