---
title: 'Tutorial 2: Treinar um modelo de regressão com aprendizado de máquina automatizado – serviço Azure Machine Learning'
description: Saiba como gerar um modelo de ML usando o aprendizado de máquina automatizado.  O Azure Machine Learning pode executar o pré-processamento de dados, a seleção de algoritmo e a seleção de hiperparâmetro de maneira automatizada para você. O modelo final então será implantado com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 0c4b9c31c4ae8a6a7a7044887c9af051966c745e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879524"
---
# <a name="tutorial-2-train-a-regression-model-with-automated-machine-learning"></a>Tutorial 2: Treinar um modelo de regressão com aprendizado de máquina automatizado

Este tutorial é **parte dois de uma série de tutoriais de duas partes**. No tutorial anterior, você [preparou os dados de táxis da Cidade de Nova York para modelagem de regressão](tutorial-data-prep.md).

Agora, você está pronto para começar a criar seu modelo com o serviço Azure Machine Learning. Nesta parte do tutorial, você usará os dados preparados e gerará automaticamente um modelo de regressão para prever os preços das corridas de táxi. Usando os recursos de ML automatizados do serviço, defina restrições e metas do aprendizado de máquina, inicie o processo de aprendizado de máquina automatizado e permita a seleção de algoritmo e o ajuste dos hiperparâmetros. A técnica de ML automatizado itera em muitas combinações de algoritmos e hiperparâmetros até encontrar o melhor modelo com base em seu critério.

![diagrama de fluxo](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar um ambiente de Python e importar os pacotes do SDK
> * Configurar um espaço de trabalho do serviço Azure Machine Learning
> * Treinar automaticamente um modelo de regressão
> * Executar o modelo localmente com parâmetros personalizados
> * Explorar os resultados
> * Registrar o melhor modelo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLfree) antes de começar.

>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.0 do SDK do Azure Machine Learning


## <a name="prerequisites"></a>Pré-requisitos

> * [Executar o tutorial de preparação de dados](tutorial-data-prep.md)
> * Ambiente configurado de aprendizado de máquina automatizado, por exemplo, o Azure Notebooks, um ambiente Python local ou uma Máquina Virtual de Ciência de Dados. [Configurar](samples-notebooks.md) o aprendizado de máquina automatizado.

## <a name="get-the-notebook"></a>Obter o bloco de anotações

Para sua conveniência, este tutorial está disponível como um [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb). Execute o Notebook `regression-part2-automated-ml.ipynb` em Azure Notebooks ou em seu próprio servidor de Jupyter Notebook.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Importar pacotes
Importe os pacotes do Python de que você precisa nesta sessão.


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging

```

## <a name="configure-workspace"></a>Configurar o workspace

Crie um objeto de workspace a partir do workspace existente. Um `Workspace` é uma classe que aceita sua assinatura do Azure e informações sobre o recurso e cria um recurso de nuvem para monitorar e acompanhar a execução de seu modelo. `Workspace.from_config()` lê o arquivo **aml_config/config.json** e carrega os detalhes em um objeto chamado `ws`.  `ws` é usado em todo o restante do código neste tutorial.

Depois que você tiver um objeto de workspace, especifique um nome para o experimento e crie e registre um diretório local com o workspace. O histórico de todas as execuções é registrado no experimento especificado e no [portal do Azure](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

import os

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Explorar dados

Utilize o objeto de fluxo de dados criado no tutorial anterior. Abra e execute o fluxo de dados e analise os resultados.


```python
import azureml.dataprep as dprep
package_saved = dprep.Package.open(".\dflow")
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tipo</th>
      <th>Min</th>
      <th>max</th>
      <th>Contagem</th>
      <th>Contagem faltando</th>
      <th>Sem contagem faltando</th>
      <th>Percentual faltando</th>
      <th>Contagem de erros</th>
      <th>Contagem vazia</th>
      <th>0,1% quantil</th>
      <th>1% quantil</th>
      <th>5% quantil</th>
      <th>25% quantil</th>
      <th>50% quantil</th>
      <th>75% quantil</th>
      <th>95% quantil</th>
      <th>99% quantil</th>
      <th>99,9% quantil</th>
      <th>Média</th>
      <th>Desvio padrão</th>
      <th>Variação</th>
      <th>Distorção</th>
      <th>Curtose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>fornecedor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Friday</td>
      <td>Quarta-feira</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>3,57523</td>
      <td>3</td>
      <td>9,91106</td>
      <td>15,9327</td>
      <td>19</td>
      <td>22,0225</td>
      <td>23</td>
      <td>23</td>
      <td>14,2326</td>
      <td>6,34926</td>
      <td>40,3131</td>
      <td>-0,693335</td>
      <td>-0,459336</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,32313</td>
      <td>4,92308</td>
      <td>14,2214</td>
      <td>29,5244</td>
      <td>44,6436</td>
      <td>56,3767</td>
      <td>58,9798</td>
      <td>59</td>
      <td>29,4635</td>
      <td>17,4396</td>
      <td>304,14</td>
      <td>0,00440324</td>
      <td>-1,20458</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>4,99286</td>
      <td>4,91954</td>
      <td>14,6121</td>
      <td>29,9239</td>
      <td>44,5221</td>
      <td>56,6792</td>
      <td>59</td>
      <td>59</td>
      <td>29,6225</td>
      <td>17,3868</td>
      <td>302,302</td>
      <td>-0,0227466</td>
      <td>-1,19409</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Friday</td>
      <td>Quarta-feira</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>3,23217</td>
      <td>2,93333</td>
      <td>9,92334</td>
      <td>15,9135</td>
      <td>19</td>
      <td>22,2739</td>
      <td>23</td>
      <td>23</td>
      <td>14,1815</td>
      <td>6,45578</td>
      <td>41,677</td>
      <td>-0,691001</td>
      <td>-0,500215</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,1064</td>
      <td>5</td>
      <td>14,2051</td>
      <td>29,079</td>
      <td>44,2937</td>
      <td>56,6338</td>
      <td>58,9984</td>
      <td>59</td>
      <td>29,353</td>
      <td>17,4241</td>
      <td>303,598</td>
      <td>0,0142562</td>
      <td>-1,21531</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,03373</td>
      <td>5</td>
      <td>14,7471</td>
      <td>29,598</td>
      <td>45,3216</td>
      <td>56,1044</td>
      <td>58,9728</td>
      <td>59</td>
      <td>29,7923</td>
      <td>17,481</td>
      <td>305,585</td>
      <td>-0,0281313</td>
      <td>-1,21965</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>S</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0782</td>
      <td>-73,7365</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,0763</td>
      <td>-73,9625</td>
      <td>-73,9629</td>
      <td>-73,949</td>
      <td>-73,9279</td>
      <td>-73,8667</td>
      <td>-73,8304</td>
      <td>-73,8232</td>
      <td>-73,7698</td>
      <td>-73,9139</td>
      <td>0,0487111</td>
      <td>0,00237277</td>
      <td>0,402697</td>
      <td>-0,613516</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5755</td>
      <td>40,8799</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,6329</td>
      <td>40,7131</td>
      <td>40,7116</td>
      <td>40,7214</td>
      <td>40,7581</td>
      <td>40,8051</td>
      <td>40,8489</td>
      <td>40,8676</td>
      <td>40,8777</td>
      <td>40,7652</td>
      <td>0,0483485</td>
      <td>0,00233758</td>
      <td>0,228088</td>
      <td>-0,598862</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0857</td>
      <td>-73,7209</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,0788</td>
      <td>-73,9856</td>
      <td>-73,9858</td>
      <td>-73,959</td>
      <td>-73,9367</td>
      <td>-73,8848</td>
      <td>-73,8155</td>
      <td>-73,7767</td>
      <td>-73,7335</td>
      <td>-73,9207</td>
      <td>0,055961</td>
      <td>0,00313163</td>
      <td>0,648649</td>
      <td>0,0229141</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5835</td>
      <td>40,8797</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,5977</td>
      <td>40,6954</td>
      <td>40,6951</td>
      <td>40,7275</td>
      <td>40,7582</td>
      <td>40,7884</td>
      <td>40,8504</td>
      <td>40,868</td>
      <td>40,8786</td>
      <td>40,7595</td>
      <td>0,0504621</td>
      <td>0,00254642</td>
      <td>0,0484179</td>
      <td>-0,0368799</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2,32979</td>
      <td>1,79978</td>
      <td>3,2392</td>
      <td>0,834099</td>
      <td>-1,11111</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>444</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>3,01808</td>
      <td>3,0125</td>
      <td>5,91545</td>
      <td>9,49055</td>
      <td>16,5816</td>
      <td>33,5638</td>
      <td>51,9924</td>
      <td>81,1368</td>
      <td>12,9112</td>
      <td>11,6447</td>
      <td>135,599</td>
      <td>8,6842</td>
      <td>269,818</td>
    </tr>
  </tbody>
</table>

Prepare os dados para o experimento adicionando colunas a `dflow_x` como recursos para a criação de nosso modelo. Defina `dflow_y` como nosso valor de previsão; custo.

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday', 'dropoff_latitude', 'dropoff_longitude','pickup_hour','pickup_longitude','pickup_latitude','passengers'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-data-into-train-and-test-sets"></a>Dividir os dados em conjuntos de treinamento e teste

Agora, divida os dados em conjuntos de treinamento e teste usando a função `train_test_split` na biblioteca `sklearn`. Essa função separa os dados em conjunto de dados X (recursos) para treinamento do modelo, e conjunto de dados Y (valores a serem previstos) para teste. O parâmetro `test_size` determina a porcentagem de dados a ser alocada para teste. O parâmetro `random_state` define uma semente para o gerador aleatório, de modo que suas divisões de teste e treinamento sejam sempre determinísticas.

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=123)
# flatten y_train to 1d array
y_train.values.flatten()
```

Agora, você tem os pacotes e os dados necessários prontos para treinamento automática para o seu modelo.

## <a name="automatically-train-a-model"></a>Treinar um modelo automaticamente

Para treinar um modelo automaticamente:
1. Defina configurações para a execução do experimento
1. Envie o experimento para ajuste do modelo

### <a name="define-settings-for-autogeneration-and-tuning"></a>Defina configurações para geração automática e ajuste

Defina as configurações de modelos e os parâmetros do experimento para geração automática e ajuste. Exiba a lista completa das [configurações](how-to-configure-auto-train.md).


|Propriedade| Valor neste tutorial |DESCRIÇÃO|
|----|----|---|
|**iteration_timeout_minutes**|10|Limite de tempo, em minutos, para cada iteração|
|**iterações**|30|Número de iterações. Em cada iteração, o modelo treina com os dados com um pipeline específico|
|**primary_metric**| spearman_correlation | Métrica que você deseja otimizar.|
|**preprocess**| True | True habilita o experimento a executar o pré-processamento na entrada.|
|**verbosity**| logging.INFO | Controla o nível de registro em log.|
|**n_cross_validationss**|5|Número de divisões de validação cruzada



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```


```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Treinar o modelo de regressão automática

Inicie o experimento a ser executado localmente. Transmita o objeto `automated_ml_config` definido para o experimento e defina a saída como `true` para exibir o andamento durante o experimento.


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

    Parent Run ID: AutoML_83117da4-07e3-473a-b83e-99471bfa9e09
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************
    
     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:21       0.6498    0.6498
             1   MinMaxScaler GradientBoosting                  0:00:22       0.6624    0.6624
             2   StandardScalerWrapper KNN                      0:00:18       0.7267    0.7267
             3   StandardScalerWrapper GradientBoosting         0:00:18       0.5003    0.7267
             4    Ensemble                                      0:00:38       0.6659    0.7267
    

## <a name="explore-the-results"></a>Explorar os resultados

Explore os resultados do treinamento automático com um widget do Jupyter ou examinando o histórico do experimento.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Opção 1: Adicionar um widget do Jupyter para ver os resultados

Se você estiver usando um Jupyter Notebook, use esse widget do Jupyter Notebook para ver um gráfico e uma tabela com todos os resultados.


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Detalhes da execução do widget do Jupyter](./media/tutorial-auto-train-models/jup-widget-auto.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Opção 2: Obter e examinar todas as iterações em execução em Python

Como alternativa, você pode recuperar o histórico de cada experimento e explorar as métricas individuais de cada execução de iteração.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```




<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,113810</td>
      <td>0,093514</td>
      <td>-0,010248</td>
      <td>0,005867</td>
      <td>0,108187</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>7,004893</td>
      <td>6,348354</td>
      <td>6,493000</td>
      <td>7,045597</td>
      <td>6,646850</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>4,834063</td>
      <td>3,503244</td>
      <td>3,321553</td>
      <td>4,349547</td>
      <td>4,389995</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0,077832</td>
      <td>0,070537</td>
      <td>0,072144</td>
      <td>0,078284</td>
      <td>0,073854</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,053712</td>
      <td>0,038925</td>
      <td>0,036906</td>
      <td>0,048328</td>
      <td>0,048778</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0,117819</td>
      <td>0,120518</td>
      <td>0,126141</td>
      <td>0,124289</td>
      <td>0,118340</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0,177689</td>
      <td>0,163360</td>
      <td>0,168101</td>
      <td>0,178250</td>
      <td>0,168685</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0,104661</td>
      <td>0,064075</td>
      <td>-0,036158</td>
      <td>-0,004403</td>
      <td>0,096976</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>10,603744</td>
      <td>10,846632</td>
      <td>11,352731</td>
      <td>11,185972</td>
      <td>10,650593</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,801531</td>
      <td>0,736896</td>
      <td>0,758279</td>
      <td>0,804062</td>
      <td>0,760913</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0,549825</td>
      <td>0,562435</td>
      <td>0,526702</td>
      <td>0,500302</td>
      <td>0,565857</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,549825</td>
      <td>0,562435</td>
      <td>0,562435</td>
      <td>0,562435</td>
      <td>0,565857</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="retrieve-the-best-model"></a>Recuperar o melhor modelo

Selecione o melhor pipeline de nossas iterações. O método `get_output` no `automl_classifier` retorna a melhor execução e o modelo ajustado para a última invocação de ajuste. Há sobrecargas em `get_output` que permitem a recuperação dos melhores, e mais ajustados, modelos de uma métrica registrada ou iteração específica.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="register-the-model"></a>Registre o modelo

Registre o modelo em seu espaço de trabalho do serviço Azure Machine Learning.


```python
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model-accuracy"></a>Testar a precisão do melhor modelo

Use o melhor modelo para executar previsões no conjunto de dados de teste. A função `predict` usa o melhor modelo e prevê os valores de Y (custo da corrida) do conjunto de dados `x_test`. Imprima os 10 primeiros valores de custo previstos de `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Compare os valores de custo previstos com os valores de custo reais. Use o dataframe `y_test` e converta-o em uma lista para comparar com os valores previstos. A função `mean_absolute_error` usa duas matrizes de valores e calcula o desvio de valor absoluto médio entre elas. Neste exemplo, um desvio de valor absoluto médio de 3,5 significaria que, em média, o modelo prevê o custo até mais ou menos 3,5 do valor real.

```python
from sklearn.metrics import mean_absolute_error

y_actual = y_test.values.flatten().tolist()
mean_absolute_error(y_actual, y_predict)
```

    [ 3.16213051 ]

Execute o código a seguir para calcular o MAPE (desvio percentual absoluto médio) usando os conjuntos de dados `y_actual` e `y_predict` completos. Essa métrica calcula uma diferença absoluta entre cada valor previsto e real, soma todas as diferenças e expressa a soma como uma porcentagem do total dos valores reais.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.22424976634422172

    Model Accuracy:
    0.7757502336557782

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de aprendizado de máquina automatizado, você:

> [!div class="checklist"]
> * Configurou um espaço de trabalho e preparou os dados para um experimento
> * Treinou usando um modelo de regressão automatizado localmente com parâmetros personalizados
> * Explorou e revisou os resultados do treinamento
> * Registrou o melhor modelo

[Implantar seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
