---
title: Interpretabilidade do modelo
titleSuffix: Azure Machine Learning service
description: Saiba como explicar por que o seu modelo faz previsões usando o SDK do Azure Machine Learning. Ele pode ser usado durante a inferência e treinamento para entender como seu modelo faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/29/2019
ms.openlocfilehash: 62d51a0075d8b6864e4b10fa6c1eb423a440d6d0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926459"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Possibilidade de interpretação de modelo com o serviço Azure Machine Learning

Neste artigo, você aprenderá a explicar por que o seu modelo de feitas as previsões com o pacote de possibilidade de interpretação do SDK de Python do Azure Machine Learning.

Usando as classes e métodos deste pacote, você pode obter:
+ Possibilidade de interpretação em conjuntos de dados do mundo real em grande escala, durante o tempo de treinamento e Inferência. 
+ Visualizações interativas para ajudar você a descoberta de padrões nos dados e explicações em tempo de treinamento
+ Os valores de importância de recursos: brutos e engenharia de recursos

Durante a fase de treinamento do ciclo de desenvolvimento, os avaliadores e designers de modelo podem usar para explicar a saída de um modelo aos participantes para criar a relação de confiança.  Eles também usam as informações sobre o modelo para depuração, validar o comportamento do modelo corresponde a seus objetivos e verificar a tendência.

Durante a fase de inferência, cientistas de dados podem usar a possibilidade de interpretação para explicar as previsões para as pessoas que usam seu modelo. Por exemplo, por que o modelo negar um empréstimo de hipoteca ou prever que um portfólio de investimento traz um risco mais alto?

Usando essas ofertas, você pode explicar os modelos de aprendizado de máquina **globalmente em todos os dados**, ou **localmente em um ponto de dados específicos** usando as tecnologias de última geração de uma maneira fácil de usar e escalonável.

As classes de possibilidade de interpretação são disponibilizadas por meio de dois pacotes do Python. Saiba como [instalar pacotes SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), o pacote principal, que contém as funcionalidades com suporte da Microsoft. 

* `azureml.contrib.explain.model`, visualização e as funcionalidades experimentais que você pode tentar.

> [!IMPORTANT]
> As coisas em contrib não são totalmente compatíveis. Como as funcionalidades experimentais ficam maduras, eles serão movidos gradualmente para o pacote principal.

## <a name="how-to-interpret-your-model"></a>Como interpretar seu modelo

Você pode aplicar as classes de possibilidade de interpretação e métodos para entender o comportamento de global do modelo ou previsões específicos. O primeiro é chamado de explicação global e o último é chamado de explicação local.

Os métodos podem ser categorizados também com base em se o método é independente de modelo ou um modelo específico. Alguns métodos de determinado tipo de modelos de destino. Por exemplo, explicador de árvore da forma só se aplica a modelos baseados em árvore. Alguns métodos de tratam o modelo como uma caixa preta, como imitar explicador ou explicador do kernel da forma. O `explain` pacote utiliza essas abordagens diferentes com base em conjuntos de dados, tipos de modelo e casos de uso. 

A saída é um conjunto de informações sobre como um determinado modelo torna sua previsão, como:
* Importância do recurso relativo do local/global

* Relação de recurso e previsão global/local

### <a name="explainers"></a>Explainers

Há dois conjuntos de explainers: Explainers diretas e Explainers de Meta no SDK.

__Direcionar explainers__ vêm de bibliotecas integradas. O SDK encapsula todos os explainers para que eles expõem uma API comum e o formato de saída. Se você estiver mais familiarizado com o uso diretamente esses explainers, você pode invocá-los diretamente em vez de usar a API comum e o formato de saída. A seguir é uma lista das explainers diretas disponíveis no SDK:

* **Árvore explicador**: Explicador de árvore da forma, que se concentra no tempo polinomial rápido da forma valor estimativa específico de algoritmo de árvores e conjuntos de árvores de.
* **Explicador profunda**: Com base na explicação da forma, explicador profunda "é um algoritmo de aproximação de alta velocidade de valores da forma de modelos de aprendizado profundo que se baseia em uma conexão com DeepLIFT descrita no artigo NIPS da forma. Há suporte para modelos de TensorFlow e modelos de Keras usando o back-end TensorFlow (também há suporte preliminar para PyTorch) ".
* **Explicador kernel**: Explicador de Kernel da forma usa uma regressão linear de local especialmente ponderada para estimar os valores da forma para qualquer modelo.
* **Imitar explicador**: Explicador imitar baseia-se na ideia de modelos globais substituto. Um modelo de substitutos global é um modelo que é interpretado intrinsecamente que é treinado para aproximar as previsões de um modelo de caixa preta de forma mais precisa possível. Cientista de dados pode interpretar o modelo de substitutos para tirar conclusões sobre o modelo de caixa preta.
* **Verde-LIMÃO explicador** (`contrib`): Verde-LIMÃO explicador com base em verde-LIMÃO, usa o algoritmo de geração de Local que é interpretado independente de modelo explicações (verde-LIMÃO) para criar modelos de local alternativo. Ao contrário dos modelos de substitutos global, verde-LIMÃO concentra-se no treinamento de modelos de local alternativo para explicar as previsões individuais.
* **HENRIQUE texto explicador** (`contrib`): HENRIQUE texto explicador usa uma rede de atenção hierárquica para obter explicações sobre o modelo de dados de texto para um modelo de texto determinada caixa-preta. Treinar o modelo de substitutos HENRIQUE nas saídas de um modelo professor determinado previsto. Após o treinamento globalmente em corpus de texto, adicionamos uma etapa de ajuste fino de um documento específico para melhorar a precisão das explicações. HENRIQUE usa um bidirecional RNN com duas camadas de atenção, por atenção sentença e palavra. Depois que a DNN é treinada em um modelo de professor e ajustada em um documento específico, podemos pode extrair os importances word das camadas de atenção. Descobrimos HENRIQUE para ser mais precisos do que verde-LIMÃO ou da forma para dados de texto, mas mais caros em termos de tempo também de treinamento. No entanto, fizemos melhorias para o tempo de treinamento por dar ao usuário a opção para inicializar a rede com luva incorporações de palavras, embora ele ainda está lento. O tempo de treinamento pode ser melhorado significativamente executando HENRIQUE em uma VM de GPU remota do Azure. A implementação de HENRIQUE é descrita em 'Redes de atenção hierárquica para classificação de documento (Yang farão, 2016)' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Explainers meta__ automaticamente selecionar um explicador direto adequado e gerar as informações de explicação melhor com base no modelo especificado e conjuntos de dados. Os explainers meta aproveitam todas as bibliotecas (da forma, verde-LIMÃO, imitam, etc.) que temos integrado ou desenvolvidos. Estes são os explainers meta disponíveis no SDK:

* **Explicador tabular**: Usado com conjuntos de dados tabulares.
* **Texto explicador**: Usado com conjuntos de dados de texto.

Além ao meta-selecionando dos explainers diretos, explainers meta desenvolvem recursos adicionais sobre as bibliotecas subjacentes e melhorar a velocidade e escalabilidade nos explainers diretos.

No momento `TabularExplainer` emprega a lógica a seguir para invocar o Explainers direto:

1. Se for um modelo baseado em árvore, aplicar `TreeExplainer`, else
2. Se for um modelo DNN, aplicar `DeepExplainer`, else
3. Tratá-lo como um modelo de caixa preta e aplicar `KernelExplainer`

A inteligência incorporada `TabularExplainer` se tornará mais sofisticadas conforme mais bibliotecas estão integradas no SDK e podemos aprender sobre os prós e contras de cada explicador.

`TabularExplainer` também fez aprimoramentos significativos de desempenho e de recursos sobre o Explainers direto:

* **Resumo do conjunto de dados de inicialização**. Em casos onde a velocidade de explicação é mais importante, vamos resumir o conjunto de dados de inicialização e gerar um pequeno conjunto de exemplos representativos, que acelera a explicação global e local.
* **O conjunto de dados de avaliação de amostragem**. Se o usuário passa em um grande conjunto de exemplos de avaliação, mas, na verdade, não precisa de todos eles a ser avaliada, o parâmetro de amostragem pode ser definido como true para acelerar a explicação global.

O diagrama a seguir mostra a relação entre os dois conjuntos de direct e explainers meta.

[![Arquitetura de possibilidade de interpretação de aprendizado de máquina](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modelos com suporte

Os modelos são treinados em conjuntos de dados em Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, ou `scipy.sparse.csr_matrix` formato são compatíveis com a possibilidade de interpretação `explain` pacote do SDK.

As funções de explicação aceitam modelos e pipelines como entrada. Se um modelo for fornecido, o modelo deve implementar a função de previsão `predict` ou `predict_proba` que esteja de acordo com a convenção de Scikit. Se um pipeline (nome do script de pipeline) for fornecido, a função de explicação pressupõe que o script em execução do pipeline retorna uma previsão.

### <a name="local-and-remote-compute-target"></a>Destino de computação local e remota

O `explain` pacote foi projetado para funcionar com ambos os destinos de computação local e remota. Se executar localmente, as funções do SDK não contatar todos os serviços do Azure. Você pode executar explicação remotamente na computação do Azure Machine Learning e registrar as informações na explicação em serviços de histórico de execução do Azure Machine Learning. Depois que essas informações são registradas, relatórios e visualizações da explicação estão prontamente disponíveis no portal de espaço de trabalho do Azure Machine Learning para análise de usuário.

## <a name="interpretability-in-training"></a>Possibilidade de interpretação em treinamento

### <a name="train-and-explain-locally"></a>Treinar e explique localmente

1. Treine seu modelo em um notebook Jupyter local. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chame o explicador: Para inicializar um objeto explicador, você precisa passar seu modelo e alguns dados de treinamento para o construtor do explicador. Você também pode passar em nomes de recurso e saída nomes de classe (se estiver fazendo classificação) que serão usados para tornar suas visualizações e explicações mais informativo. Aqui está como criar uma instância de um objeto explicador usando [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) e [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) localmente. `TabularExplainer` é chamar um dos três explainers abaixo (`TreeExplainer`, `DeepExplainer`, ou `KernelExplainer`) e é automaticamente selecionando o mais apropriado para seu caso de uso. No entanto, você pode chamar cada um dos seus três explainers subjacentes diretamente.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    ou o
    
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Obter o recurso global de valores de importância.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Os valores de importância do recurso local: use as seguintes chamadas de função para explicar a uma instância individual ou um grupo de instâncias.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    ou o
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Treinar e explique remotamente

Enquanto você pode treinar sobre os vários destinos de computação com suporte pelo serviço Azure Machine Learning, o exemplo nesta seção mostra como fazer isso usando um destino de computação do Azure Machine Learning.

1. Crie um script de treinamento em um bloco de anotações do Jupyter (por exemplo, run_explainer.py) local.

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine   
    # "features" and "classes" fields are optional 
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Siga as instruções em [configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#amlcompute) para saber mais sobre como configurar uma computação do Azure Machine Learning como seu destino de computação e enviar sua execução de treinamento.

3. Baixe a explicação do bloco de anotações Jupyter local. 


    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Visualizações

Use o painel de visualização para entender e interpretar seu modelo:

### <a name="global-visualizations"></a>Visualizações globais

Os gráficos a seguir fornecem uma visão global do modelo treinado, juntamente com suas previsões e explicações.

|Gráfico |DESCRIÇÃO|
|----|-----------|
|Exploração de dados| Uma visão geral do conjunto de dados, juntamente com os valores de previsão.|
|Importância global|Mostra os principais recursos importantes do K (K configurável) globalmente. Este gráfico é útil para entender o comportamento global do modelo subjacente.|
|Exploração de explicação|Demonstra como um recurso é responsável por fazer uma alteração nos valores de previsão do modelo (ou a probabilidade de valores de previsão). |
|Resumo| Usa um valores de importância do recurso local assinado em todos os pontos de dados para mostrar a distribuição do impacto de que cada recurso tem o valor de previsão.|

[![Painel de visualização Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizações locais
Você pode clicar em qualquer ponto de dados individuais a qualquer momento os gráficos anteriores para carregar o gráfico de importância do recurso local para o ponto de dados específico.

|Gráfico |DESCRIÇÃO|
|----|-----------|
|Importância local|Mostra os principais recursos importantes do K (K configurável) globalmente. Este gráfico é útil para entender o comportamento local do modelo subjacente em um ponto de dados específico.|

[![Local do painel de visualização](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

Para carregar o painel de visualização, use o seguinte código:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
``` 

## <a name="raw-feature-transformations"></a>Transformações de recursos brutos

Opcionalmente, você pode passar seu pipeline de transformação de recurso para o explicador para receber explicações em termos dos recursos brutos antes da transformação (em vez de recursos de engenharia). Se você ignorar essa etapa, o explicador fornece explicações em termos de recursos de engenharia. 

O formato de transformações com suporte é o mesmo como aquele descrito em [sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Em geral, todas as transformações têm suporte contanto que eles operam em uma única coluna e, portanto, são claramente um para muitos.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inferencing"></a>Possibilidade de interpretação de inferência

O explicador pode ser implantado junto com o modelo original e pode ser usado em vez de pontuação para fornecer as informações de local de explicação. O processo de implantação de uma pontuação explicador é semelhante à implantação de um modelo e inclui as seguintes etapas:

1. Crie um objeto de explicação:
   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ``` 

1. Crie uma pontuação explicador usando o objeto de explicação:
   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ``` 

1. Configurar e registrar uma imagem que usa o modelo de pontuação do explicador.
   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ``` 

1. [Opcional] Recuperar o pontuação explicador da nuvem e as explicações de teste
   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retreive the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implante a imagem em um destino de computação:

   1. Criar um arquivo de pontuação (antes desta etapa, siga as etapas em [implantar modelos com o serviço de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) para registrar o seu modelo de previsão original)
        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ``` 
    1. Definir a configuração de implantação (essa configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que usa um núcleo de CPU e 1 GB de memória)
        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                       memory_gb=1, 
                                                       tags={"data": "breastcancer",  
                                                             "method" : "local_explanation"}, 
                                                       description='Get local explanations for breast cancer data')
        ``` 

    1. Crie um arquivo com as dependências do ambiente

        ```python
        from azureml.core.conda_dependencies import CondaDependencies 

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"], 
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())
            
        with open("myenv.yml","r") as f:
            print(f.read())
        ``` 
    1. Criar um dockerfile personalizado com instalado g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++  
        ``` 
    1. Implantar a imagem criada (tempo estimado: 5 minutos)
        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile", 
                                                        runtime="python", 
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ``` 

1. Teste a implantação
    ```python
    import requests

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ``` 

1. Limpe: Para excluir um serviço Web implantado, use `service.delete()`.

## <a name="next-steps"></a>Próximas etapas

Para ver uma coleção de blocos de anotações do Jupyter que demonstram as instruções acima, consulte a [notebooks de amostra da possibilidade de interpretação do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
