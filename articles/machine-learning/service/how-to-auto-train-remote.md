---
title: Configurar destinos de computação remota para aprendizado de máquina automatizado – serviços do Azure Machine Learning
description: Este artigo explica como criar modelos usando o aprendizado de máquina automatizado em um destino de computação remota DSVM (Máquina Virtual de Ciência de Dados) com os serviços do Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 2ec0dea7e50747f8af337874c8f12463cecb8df7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163470"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Treinar modelos com o aprendizado de máquina automatizado na nuvem

No Azure Machine Learning, você pode treinar seu modelo em diferentes tipos de recursos de computação que você gerencia. O destino de computação pode ser um computador local ou um computador na nuvem.

Você pode escalar seu experimento de aprendizado de máquina vertical ou horizontalmente com facilidade adicionando mais destinos de computação como DSVM (Máquina Virtual de Ciência de Dados) com base em Ubuntu ou IA do Lote do Azure. A DSVM é uma imagem de VM personalizada na nuvem do Microsoft Azure especificamente criada para ciência de dados. Ele tem muitas ferramentas de ciência de dados populares e outras pré-instaladas e pré-configuradas.  

Neste artigo, você aprende a criar um modelo usando o aprendizado de ML na DSVM. Você pode encontrar exemplos usando a IA do Lote do Azure [nesses notebooks de exemplo no GitHub](https://aka.ms/aml-notebooks).  

## <a name="how-does-remote-differ-from-local"></a>Como remoto difere do local?

O tutorial "[Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md)" ensina a usar um computador local para treinar o modelo com ML automatizado.  O fluxo de trabalho durante o treinamento local também se aplica aos destinos remotos. No entanto, com a computação remota, as iterações de experimento de ML automatizado são executadas de maneira Assíncrona. Isso permite cancelar uma iteração específica, observar o status da execução ou continuar a trabalhar em outras células no Jupyter Notebook. Para treinar remotamente, você primeiro cria um destino de computação remota, como uma DSVM do Azure.  Em seguida, configura o recurso remoto e envia seu código para ele.

Este artigo mostra as etapas adicionais necessárias para executar um experimento de ML automatizado em uma DSVM remota.  O objeto de workspace, `ws`, do tutorial, é usado em todo o código aqui.

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
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
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

Esse código não cria um nome de usuário ou senha para a DSVM provisionada. Se você quiser se conectar diretamente à VM, acesse o [portal do Azure](https://portal.azure.com) para provisionar as credenciais.  


## <a name="access-data-using-getdata-file"></a>Acessar dados usando o arquivo get_data

Forneça ao recurso remoto acesso aos seus dados de treinamento. Para experimentos de aprendizado de máquina automatizados em execução em computação remota, os dados precisam ser buscados usando uma função `get_data()`.  

Para fornecer acesso, você deve:
+ Criar um arquivo get_data.py contendo uma função `get_data()` 
* Coloque esse arquivo no diretório raiz da pasta que contém seus scripts 

Você pode encapsular o código para ler dados de um armazenamento de blobs ou de um disco local no arquivo get_data.py. No exemplo de código a seguir, os dados vêm do pacote sklearn.

>[!Warning]
>Se você está usando computação remota, deve usar `get_data()` no local em que suas transformações de dados são realizadas. Se você precisa instalar bibliotecas adicionais para transformações de dados como parte do get_data(), há etapas adicionais a serem seguidas. Consulte o [notebook de exemplo auto-ml-dataprep](https://aka.ms/aml-auto-ml-data-prep ) para encontrar detalhes.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
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

## <a name="configure-experiment"></a>Configurar o experimento

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
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-training-experiment"></a>Enviar o teste de treinamento

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

O bloco `automl/03.auto-ml-remote-execution.ipynb` demonstra conceitos neste artigo.  Veja este caderno:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba [como definir as configurações para treinamento automático](how-to-configure-auto-train.md).