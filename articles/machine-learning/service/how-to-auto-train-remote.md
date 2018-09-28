---
title: Treinar modelos com o aprendizado de máquina automatizado na nuvem – Azure Machine Learning
description: Este artigo explica como criar um recurso de computação remota para treinar automaticamente seus modelos de machine learning.
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 00d34fd0fe5f62e4da4be7d80afceb29753251bc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946962"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud-with-azure-machine-learning"></a>Treinar modelos com o aprendizado de máquina automatizado na nuvem com o Azure Machine Learning

No Azure Machine Learning, você pode treinar seu modelo em diferentes tipos de recursos de computação que você gerencia. O destino de computação pode ser um computador local ou um computador na nuvem.

Você pode escalar seu experimento de aprendizado de máquina vertical ou horizontalmente com facilidade adicionando mais destinos de computação como DSVM (Máquina Virtual de Ciência de Dados) com base em Ubuntu ou IA do Lote do Azure. A DSVM é uma imagem de VM personalizada na nuvem do Microsoft Azure especificamente criada para ciência de dados. Ele tem muitas ferramentas de ciência de dados populares e outras pré-instaladas e pré-configuradas.  

Neste artigo, você aprende a criar um modelo usando o aprendizado de máquina automatizado na DSVM.  

## <a name="how-does-remote-differ-from-local"></a>Como remoto difere do local?

O tutorial "[Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md)" ensina a usar um computador local para treinar o modelo com aprendizado de máquina automatizado.  O fluxo de trabalho durante o treinamento local também se aplica aos destinos remotos. Com a computação remota, iterações de experimento de aprendizado de máquina automatizado são executadas de maneira Assíncrona. Isso permite cancelar uma iteração específica, observar o status da execução e continuar a trabalhar em outras células no Jupyter Notebook. Para treinar remotamente, você primeiro cria um destino de computação remota, como uma DSVM do Azure, configura-o e envia seu código lá.

Este artigo mostra as etapas adicionais necessárias para executar o experimento de aprendizado de máquina automatizado uma DSVM remota.  O objeto de workspace, `ws`, do tutorial, é usado em todo o código aqui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Criar recurso

Crie a DSVM em seu workspace (`ws`) se ela ainda não existir. Caso a DSVM tenha sido criada anteriormente, esse código ignorará o processo de criação e carregará os detalhes de recursos existentes para o objeto `dsvm_compute`.  

**Tempo estimado**: a criação da VM leva aproximadamente 5 minutos.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Agora você pode usar o objeto `dsvm_compute` como o destino de computação remota.

As restrições de nome da DSVM incluem:
+ Deve ser menor do que 64 caracteres.  
+ Não pode incluir nenhum dos seguintes caracteres: `\` ~! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Se a criação falhar com uma mensagem sobre a qualificação para compra do Marketplace:
>    1. Vá para o [Portal do Azure](https://portal.azure.com)
>    1. Comece a criar uma DSVM 
>    1. Selecione "Desejo criar programaticamente" para habilitar a criação programática
>    1. Saia sem realmente criar a VM
>    1. Execute novamente o código de criação


## <a name="create-a-runconfiguration-with-dsvm-name"></a>Criar a RunConfiguration com o nome DSVM
Aqui, você informará à runconfiguration o nome da sua dsvm

```python

# create the run configuration to use for remote training
from azureml.core.runconfig import RunConfiguration
run_config = RunConfiguration() 
# set the target to dsvm_compute created above
run_config.target = dsvm_compute 
```

Agora você pode usar o objeto `run_config` como o destino para aprendizado de máquina automatizado. 

## <a name="access-data-using-get-data-file"></a>Acessar dados usando o arquivo Obter Arquivo de dados

Forneça ao recurso remoto acesso aos seus dados de treinamento. Para experimentos de aprendizado de máquina automatizados em execução em computação remota, os dados precisam ser buscados usando uma função `get_data()`.  

Para fornecer acesso, você deve:
+ Criar um arquivo get_data.py contendo uma função `get_data()` 
* Coloque esse arquivo no diretório raiz da pasta que contém seus scripts 

Você pode encapsular o código para ler dados de um armazenamento de blobs ou de um disco local no arquivo get_data.py. No exemplo de código a seguir, os dados vêm do pacote sklearn.

>[!Warning]
>Se você está usando computação remota, você deve usar `get_data()` para realizar suas transformações de dados.


```python
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-automated-machine-learning-experiment"></a>Configurar experimento de aprendizado de máquina automatizado

Especifique as configurações para `AutoMLConfig`.  (Veja uma [lista completa de parâmetros]() e seus valores possíveis.)

Nas configurações, `run_configuration` é definido como o objeto `run_config`, que contém as definições e a configuração para a DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "max_time_sec": 600,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             run_configuration=run_config,
                             data_script=project_folder + "./get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-automated-machine-learning-training-experiment"></a>Enviar experimento de treinamento de aprendizado de máquina automatizado

Agora, envie a configuração para selecionar automaticamente o algoritmo, os hiperparâmetros e treinar o modelo. (Obtenha [mais informações sobre as configurações]() para o método `submit`.)

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```
Você verá uma saída semelhante a essa:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0.0                      0.954     0.954
             7      Normalizer DT                         0.0                      0.161     0.954
             0      Scale MaxAbs 1 extra trees            0.0                      0.936     0.954
             4      Robust Scaler SGD classifier          0.0                      0.867     0.954
             1      Normalizer kNN                        0.0                      0.984     0.984
             9      Normalizer extra trees                0.0                      0.834     0.984
             5      Robust Scaler DT                      0.0                      0.736     0.984
             8      Standardize kNN                       0.0                      0.981     0.984
             6      Standardize SVM                       2.2                      0.984     0.984
            10      Scale MaxAbs 1 DT                     0.0                      0.077     0.984
            11      Standardize SGD classifier            0.0                      0.863     0.984
             3      Standardize gradient boosting         5.4                      0.971     0.984
            12      Robust Scaler logistic regression     2.0                      0.955     0.984
            14      Scale MaxAbs 1 SVM                    0.0                      0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      3.4                      0.971     0.989
            15      Robust Scaler kNN                     0.0                      0.904     0.989
            17      Standardize kNN                       0.0                      0.974     0.989
            16      Scale 0/1 gradient boosting           2.8                      0.968     0.989
            18      Scale 0/1 extra trees                 0.0                      0.828     0.989
            19      Robust Scaler kNN                     0.0                      0.983     0.989


## <a name="explore-results"></a>Explorar os resultados

Você pode usar o mesmo widget do Jupyter que o mostrado no [tutorial de treinamento](tutorial-auto-train-models.md#explore-the-results) para ver um gráfico e uma tabela de resultados.

```python
from azureml.train.widgets import RunDetails
RunDetails(remote_run).show()
```
Aqui está uma imagem estática do widget.  No notebook, você pode clicar em qualquer linha na tabela para ver as propriedades de execução e os logs de saída para a execução.   Você também pode usar a lista suspensa acima do gráfico para exibir um gráfico de cada métrica disponível para cada iteração.

![tabela de widget](./media/how-to-auto-train-remote/table.png)
![gráfico de widget](./media/how-to-auto-train-remote/plot.png)

O widget exibirá uma URL que você pode usar para ver e explorar os detalhes individuais da execução.
 
### <a name="view-logs"></a>Exibir logs

Encontre logs na DSVM em /tmp/azureml_run/{iterationid}/azureml-logs.

## <a name="example"></a>Exemplo

O notebook `automl/03.auto-ml-remote-execution.ipynb` demonstra conceitos neste artigo.  Obtenha este notebook:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba [como definir as configurações para treinamento automático]().
