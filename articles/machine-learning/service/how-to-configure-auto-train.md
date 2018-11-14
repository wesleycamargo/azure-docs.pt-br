---
title: Configure seu experimento de aprendizado de máquina automatizado – Azure Machine Learning
description: O aprendizado de máquina automatizado escolhe um algoritmo para você e gera um modelo pronto para implantação. Saiba quais opções você pode usar para configurar experimentos de aprendizado de máquina automatizados.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5a539aebc9b1ccb5f52f89d511fc992ee6a24c81
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008939"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Configure seu experimento de aprendizado de máquina automatizado

O ML (aprendizado de máquina) automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. O modelo também pode ser baixado para ser ainda mais personalizado. Existem várias opções que você pode usar para configurar experimentos de ML automatizados. Neste guia, você aprenderá como definir vários conjuntos de configuração.

Para ver exemplos de ML automatizado, confira [Tutorial: treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [Treinar modelos com aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Seleção do tipo de experimento, por exemplo, Classificação, Regressão 
* Fonte de dados, formatos e busca de dados
* Escolha do destino de computação (local ou remoto)
* Configurações do experimento de ML automatizado
* Execução de um experimento de ML automatizado
* Exploração das métricas do modelo
* Registro e implantação do modelo

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento
Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O ML automatizado é compatível com duas categorias de aprendizado supervisionado: Classificação e Regressão. O ML automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo.
classificação | Regressão
--|--
sklearn.linear_model.LogisticRegression | sklearn.linear_model.ElasticNet
sklearn.linear_model.SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes.BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes.MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model.LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model.SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm.LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm.LGBMClassifier |


## <a name="data-source-and-format"></a>Fonte de dados e formato
O ML automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos nos formatos de dados compatíveis com scikit-learn. É possível ler os dados em 1) matrizes Numpy X (recursos) e y (variável de destino, também conhecida como rótulo) ou 2) dataframe do Pandas. 

Exemplos:

1.  Matrizes Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Dataframe do Pandas

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Buscar dados para executar o experimento em computação remota

Se você estiver usando computação remota para executar o experimento, a busca de dados precisará ser encapsulada em um script `get_data()` separado do Python. Esse script é executado na computação remota em que o experimento de ML automatizado é executado. `get_data` elimina a necessidade de buscar os dados durante a transmissão para cada iteração. Sem `get_data`, o experimento falhará ao ser executado em computação remota.

Veja um exemplo de `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
from sklearn.preprocessing import LabelEncoder 
def get_data(): # Burning man 2016 data 
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42) 
    return { "X" : df, "y" : y }
```

No objeto `AutoMLConfig`, você especifica o parâmetro `data_script` e fornece o caminho para o arquivo de script `get_data` desta forma:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

O script `get_data` pode retornar o seguinte:
Chave | Tipo |    Mutuamente exclusivo com | DESCRIÇÃO
---|---|---|---
X | Dataframe do Pandas ou matriz Numpy | data_train, rótulo, colunas |  Todos os recursos a serem treinados com
y | Dataframe do Pandas ou matriz Numpy |   label   | Rotular dados a serem treinados com. Para classificação, precisa ser uma matriz de inteiros.
X_valid | Dataframe do Pandas ou matriz Numpy   | data_train, rótulo | _Opcional_ Todos os recursos a serem validados com. Se não for especificado, X será dividido entre "treinar" e "validar"
y_valid |   Dataframe do Pandas ou matriz Numpy | data_train, rótulo | _Opcional_ Os dados de rótulo a serem validados com. Se não for especificado, y será dividido entre "treinar" e "validar"
sample_weight | Dataframe do Pandas ou matriz Numpy |   data_train, rótulo, colunas| _Opcional_ Um valor de peso para cada amostra. Use quando quiser atribuir pesos diferentes aos pontos de dados 
sample_weight_valid | Dataframe do Pandas ou matriz Numpy | data_train, rótulo, colunas |    _Opcional_ Um valor de peso para cada amostra de validação. Se não for especificado, sample_weight será dividido entre "treinar" e "validar"
data_train |    Dataframe do Pandas |  X, y, X_valid, y_valid |    Todos os dados (recursos + rótulo) a serem treinados com
label | string  | X, y, X_valid, y_valid |  Qual coluna em data_train representa o rótulo
colunas | Matriz de cadeias de caracteres  ||  _Opcional_ Lista de permissões de colunas a serem usadas para recursos
cv_splits_indices   | Matriz de inteiros ||  _Opcional_ Lista de índices para dividir os dados para validação cruzada

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

É possível especificar conjuntos de treinamento e validação por meio de get_data() ou diretamente no método `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Opções de divisão de validação cruzada

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (também conhecida como subamostragem aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizados se a divisão aleatória não for aceitável (geralmente, dados de série temporal ou dados não balanceados). Com isso, é possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um teste de treinamento de ML automatizado é executado em um destino de computação que você tem e gerencia. 

Opções de computação compatíveis são:
1.  O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
2.  Um computador remoto na nuvem – [Máquina Virtual de Ciência de Dados do Azure](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) que executa o Linux – quando o conjunto de dados é grande e você quer escalar verticalmente para um computador grande disponível na nuvem do Azure. 
3.  Cluster do IA do Lote do Azure – um cluster gerenciado que pode ser configurado para escalar horizontalmente e executar iterações de ML automatizado em paralelo. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Existem vários botões que você pode usar para configurar experimentos de ML automatizados. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`.

Alguns exemplos incluem:

1.  Experimento de classificação usando AUC ponderada como a métrica principal com um tempo máximo de 12.000 segundos por iteração, com o experimento programado para terminar após 50 iterações e duas partições de validação cruzada.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Veja abaixo um exemplo de experimento de regressão programado para terminar após 100 iterações, em que cada iteração dura até 600 segundos com cinco partições de validação cruzada.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Esta tabela lista as configurações de parâmetro disponíveis para seu experimento e seus valores padrão.

Propriedade |  DESCRIÇÃO | Valor Padrão
--|--|--
`task`  |Especifique o tipo de problema de aprendizado de máquina. Os valores permitidos são <li>classificação</li><li>regressão</li>    | Nenhum |
`primary_metric` |Métrica que você quer otimizar ao criar um modelo. Por exemplo, se você especificar a precisão como primary_metric, o ML automatizado buscará um modelo com máxima precisão. Só é possível especificar uma primary_metric por experimento. Os valores permitidos são <br/>**Classificação**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regressão**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | Para Classificação: precisão <br/>Para Regressão: spearman_correlation <br/> |
`exit_score` |  É possível definir uma meta de valor para primary_metric. Após encontrar um modelo que cumpra a meta de primary_metric, o ML automatizado parará de iterar e o experimento será encerrado. Se esse valor não for definido (padrão), o experimento do ML automatizado continuará executando o número de iterações especificado. Aceita um valor duplo. Se a meta nunca for cumprida, o ML automatizado continuará até alcançar o número de iterações especificadas.|   Nenhum
`iterations` |Número máximo de iterações. Cada iteração é igual a um trabalho de treinamento que resulta em um pipeline. O pipeline é o pré-processamento de dados e o modelo. Para obter um modelo de alta qualidade, use 250 ou mais | 100
`Concurrent_iterations`|    Número máximo de iterações a ser executado em paralelo. Essa configuração funciona somente para computação remota.|    1
`max_cores_per_iteration`   | Indica quantos núcleos seriam usados no destino de computação para treinar um único pipeline. Se o algoritmo puder aproveitar vários núcleos, isso aumentará o desempenho em um computador de vários núcleos. É possível defini-lo como -1 para usar todos os núcleos disponíveis no computador.|  1
`max_time_sec` |    Limita a quantidade de tempo (segundos) de duração de uma iteração específica. Se uma iteração exceder o valor especificado, ela será cancelada. Se não for definida, a iteração continuará sendo executada até ser encerrada. |   Nenhum
`n_cross_validations`   |Número de divisões de validação cruzada| Nenhum
`validation_size`   |Tamanho do conjunto de validação como percentual de toda a amostra de treinamento.|  Nenhum
`preprocess` | Verdadeiro/Falso <br/>True habilita o experimento a executar o pré-processamento na entrada. Veja um subconjunto de pré-processamento<li>Dados ausentes: Impute os dados ausentes - Numérico com média, texto com maior ocorrência </li><li>Valores categóricos: se o tipo de dados for numérico e o número de valores exclusivos for menor que 5%, será convertido em codificação one-hot </li><li>Etc. Para ver a lista completa, confira [o repositório GitHub](https://aka.ms/aml-notebooks)</li><br/>Observação: se os dados forem esparsos, não será possível usar o pré-processamento = true | Falso | 
`blacklist_algos`   | O experimento de ML automatizado testa vários algoritmos diferentes. Configure o ML automatizado para excluir determinados algoritmos do experimento. Essa propriedade é útil se você está ciente de que os algoritmos não funcionam bem para seu conjunto de dados. Excluir algoritmos pode economizar recursos de computação e tempo de treinamento.<br/>Valores permitidos para classificação<br/><li>Regressão logística</li><li>Classificador SGD</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>árvores extras</li><li>gradient boosting</li><li>lgbm_classifier</li><br/>Valores permitidos para regressão<br/><li>Rede elástica</li><li>Regressor de gradient boosting</li><li>Regressor DT</li><li>Regressor kNN</li><li>Lasso lars</li><li>Regressor SGD</li><li>Regressor RF</li><li>regressor de árvores extra</li>|   Nenhum
`verbosity` |Controla o nível de log com INFO sendo o mais detalhado e CRITICAL sendo o mínimo.<br/>Valores permitidos são:<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | Todos os recursos a serem treinados com |  Nenhum
`y` |   Rotular dados a serem treinados com. Para classificação, precisa ser uma matriz de inteiros.|  Nenhum
`X_valid`|_Opcional_ Todos os recursos a serem validados com. Se não for especificado, X será dividido entre "treinar" e "validar" |   Nenhum
`y_valid`   |_Opcional_ Os dados de rótulo a serem validados com. Se não for especificado, y será dividido entre "treinar" e "validar"    | Nenhum
`sample_weight` |   _Opcional_ Um valor de peso para cada amostra. Use quando quiser atribuir pesos diferentes aos pontos de dados |   Nenhum
`sample_weight_valid`   |   _Opcional_ Um valor de peso para cada amostra de validação. Se não for especificado, sample_weight será dividido entre "treinar" e "validar"   | Nenhum
`run_configuration` |   Objeto RunConfiguration.  Usado para execuções remotas. |Nenhum
`data_script`  |    Caminho para um arquivo que contém o método get_data.  Obrigatório para execuções remotas.   |Nenhum


## <a name="run-experiment"></a>Executar o experimento

Em seguida, podemos iniciar o experimento para executar e gerar um modelo. Passe o `AutoMLConfig` para o método `submit` para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Primeiro, as dependências são instaladas em uma nova DSVM.  Pode levar até dez minutos antes da saída ser exibida.
>Definir `show_output` como True faz com que a saída seja exibida no console.


## <a name="explore-model-metrics"></a>Explorar as métricas do modelo
Será possível exibir os resultados em um widget ou embutidos se você estiver usando um notebook. Confira os detalhes de como "Rastrear e avaliar modelos". Verifique se o conteúdo da AML tem informações relevantes para o ML automatizado

As métricas a seguir são salvas em cada iteração
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de classificação com ML automatizado](tutorial-auto-train-models.md) ou [como treinar usando ML automatizado em um recurso remoto](how-to-auto-train-remote.md). 