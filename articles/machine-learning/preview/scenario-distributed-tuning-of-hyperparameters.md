---
title: "Ajuste distribuído de hiperparâmetros usando o Azure Machine Learning Workbench | Microsoft Docs"
description: "Este cenário mostra como fazer o ajuste distribuído de hiperparâmetros usando o Azure Machine Learning Workbench"
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.topic: article
ms.author: dmpechyo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 4f739ff26c3df8add01bed6d797f292ff6e26db9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Ajuste distribuído de hiperparâmetros usando o Azure Machine Learning Workbench

Este cenário mostra como usar o Azure Machine Learning Workbench para aumentar o ajuste de hiperparâmetros de algoritmos de aprendizado de máquina que implementam API do scikit-learn. Mostramos como configurar e usar um contêiner remoto do Docker e um cluster Spark como um back-end de execução para ajudar hiperparâmetros.

## <a name="link-of-the-gallery-github-repository"></a>Link do repositório do GitHub da galeria
A seguir está o link para o repositório GitHub público: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Visão geral de casos de uso

Muitos algoritmos de aprendizado de máquina têm um ou mais botões, chamados hiperparâmetros. Esses botões permitem ajustar algoritmos para otimizar o desempenho em dados futuros, medidos de acordo com as métricas especificadas pelo usuário (por exemplo, precisão, AUC, RMSE). O cientista de dados precisa fornecer valores de hiperparâmetros ao criar um modelo em dados de treinamento e antes de ver os dados de teste futuros. Como, baseado nos dados de treinamento conhecidos, nós podemos configurar os valores de hiperparâmetros para que o modelo tenha um bom desempenho nos dados de teste desconhecidos? 

Uma técnica popular para ajustar hiperparâmetros é uma *pesquisa de grade* combinada com a *validação cruzada*. A validação cruzada é uma técnica que avalia quão bem um modelo, treinado em um conjunto de treinamento, é previsto no conjunto de testes. Utilizando essa técnica, primeiro dividimos o conjunto de dados em partições K e, em seguida, treinamos o algoritmo K vezes de forma round-robin. Fazemos isso em todas, exceto em uma das partições, chamada de "partição estendida". Calculamos o valor médio das métricas de modelos K em partições mantidas por K. Esse valor médio, chamado de *estimativa de desempenho de validação cruzada*, depende dos valores de hiperparâmetros usados ao criar modelos de K. Ao ajustar hiperparâmetros, pesquisamos por meio do espaço de valores de hyperparameter candidatos para localizar os que otimizam a estimativa de desempenho de validação cruzada. A pesquisa de grade é uma técnica de pesquisa comum. Na pesquisa de grade, o espaço de valores candidatos de hiperparâmetros múltiplos é um produto cruzado de conjuntos de valores candidatos de hiperparâmetros individuais. 

A pesquisa de grade usando validação cruzada pode ser demorada. Se um algoritmo tiver cinco hiperparâmetros cada um com cinco valores candidatos, usamos K = 5 partições. Em seguida, completamos uma pesquisa de grade, treinando 5<sup>6</sup>= 15625 modelos. Felizmente, a grade de pesquisa usando a validação cruzada é um procedimento constrangedoramente paralelo e todos esses modelos podem ser treinados em paralelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [Guia de início rápido de instalação e de criação](./quickstart-installation.md) para instalar o Workbench e criar contas.
* Este cenário pressupõe que você está executando o Azure ML Workbench no Windows 10 e no MacOS com o mecanismo do Docker instalado localmente. 
* Para executar o cenário com um contêiner remoto do Docker, provisione a DSVM (Máquina Virtual de Ciência de Dados) do Ubuntu seguindo as [instruções](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Recomendamos usar uma máquina virtual com pelo menos 8 núcleos e 28 Gb de memória. As instâncias D4 de máquinas virtuais têm essa capacidade. 
* Para executar esse cenário com um cluster Spark, forneça o Cluster HDInsight do Azure seguindo essas [instruções](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). É recomendável ter um cluster com pelo menos 
- seis nós de trabalho
- oito núcleos
- 28 GB de memória em ambos os nós de trabalho e cabeçalho. As instâncias D4 de máquinas virtuais têm essa capacidade. É recomendável alterar os seguintes parâmetros para maximizar o desempenho do cluster.
- spark.executor.instances
- spark.executor.cores
- spark.executor.memory 

Você pode seguir essas [instruções](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-resource-manager) e editar as definições na seção "padrões personalizados de Spark".

     **Troubleshooting**: Your Azure subscription might have a quota on the number of cores that can be used. The Azure portal does not allow the creation of cluster with the total number of cores exceeding the quota. To find you quota, go in the Azure portal to the Subscriptions section, click on the subscription used to deploy a cluster and then click on **Usage+quotas**. Usually quotas are defined per Azure region and you can choose to deploy the Spark cluster in a region where you have enough free cores. 

* Crie uma conta de armazenamento do Azure usada para armazenar o conjunto de dados. Siga as [instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento.

## <a name="data-description"></a>Descrição dos dados

Usamos o [conjunto de dados TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Este conjunto de dados tem eventos dos aplicativos em celulares. A meta é prever a categoria de sexo e de idade do usuário do celular, considerando o tipo de telefone e os eventos que o usuário gerou recentemente.  

## <a name="scenario-structure"></a>Estrutura do cenário
Este cenário tem várias pastas no repositório do GitHub. Os arquivos de código e de configuração estão na pasta **Código**, toda a documentação está na pasta **Docs** e todas as imagens estão na pasta **Imagens**. A pasta raiz tem o arquivo LEIAME que contém um breve resumo deste cenário.

### <a name="getting-started"></a>Introdução
Clique no ícone do Azure Machine Learning Workbench para executar o Azure Machine Learning Workbench e criar um projeto com base no modelo “Ajuste distribuído de hiperparâmetros”. É possível encontrar instruções detalhadas sobre como criar um novo projeto no [Guia de início rápido de instalação e de criação](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Configuração de ambientes de execução
Mostramos como executar nosso código em um contêiner remoto do Docker em um cluster Spark. Começamos com a descrição das configurações comuns a ambos os ambientes. 

Usamos os pacotes [scikit-learn](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost) e [azure-storage](https://pypi.python.org/pypi/azure-storage) que não são fornecidos no contêiner padrão do Docker do Azure Machine Learning Workbench. O pacote azure-storage requer a instalação dos pacotes [cryptography](https://pypi.python.org/pypi/cryptography) e [azure](https://pypi.python.org/pypi/azure). Para instalar esses pacotes no contêiner do Docker e nos nós do cluster Spark, modificamos o arquivo conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

O arquivo conda\_dependencies.yml modificado está armazenado no diretório aml_config do tutorial. 

Nas etapas a seguir, conectamos o ambiente de execução à conta do Azure. Clique no menu Arquivo no canto superior esquerdo do AML Workbench. E escolha "Abrir prompt de comando". Em seguida, execute na CLI

    az login

Você receberá uma mensagem

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Acesse esta página da Web, insira o código e entre em sua conta do Azure. Após essa etapa, execute na CLI

    az account list -o table

e localize a ID de inscrição do Azure que tenha sua conta do AML Workbench Workspace. Por fim, execute na CLI

    az account set -s <subscription ID>

para concluir a conexão à sua assinatura do Azure.

Nas próximas duas seções, mostremos como completar a configuração do docker remoto e do cluster Spark.

#### <a name="configuration-of-remote-docker-container"></a>Configuração do contêiner remoto do Docker

 Para configurar um contêiner remoto do Docker, execute na CLI

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

com endereço IP, nome de usuário e senha no DSVM. O endereço IP do DSVM pode ser encontrado na seção Visão geral de sua página do DSVM no Portal do Azure:

![IP de VM](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Configuração do cluster Spark

Para configurar o ambiente do Spark, execute na CLI

    az ml computetarget attach cluster--name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

com o nome do cluster, o nome de usuário e a senha SSH do cluster. O valor padrão do nome de usuário SSH é `sshuser`, a menos que você o tenha alterado durante o provisionamento do cluster. O nome do cluster pode ser encontrado na seção Propriedades da sua página de cluster no Portal do Azure:

![Nome do cluster](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Usamos o pacote spark-sklearn para ter o Spark como um ambiente de execução para ajuste distribuído de hiperparâmetros. Modificamos arquivo spark_dependencies.yml para instalar este pacote quando o ambiente de execução do Spark é usado:

    configuration: {}
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

O arquivo spark\_dependencies.yml modificado é armazenado no diretório aml_config do tutorial. 

### <a name="data-ingestion"></a>Ingestão de dados
O código neste cenário pressupõe que os dados são armazenados no armazenamento de blobs do Azure. Inicialmente, mostramos como baixar os dados do site do Kaggle para seu computador e carregá-los para o armazenamento de blobs. Em seguida, mostramos como ler os dados do armazenamento de blobs. 

Para baixar os dados do Kaggle, acesse a [página do conjunto de dados](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) e clique no botão Baixar. Você deverá fazer logon no Kaggle. Após fazer logon, você será redirecionado de volta para a página do conjunto de dados. Em seguida, baixe os primeiros sete arquivos na coluna esquerda selecionando-os e clicando no botão Baixar. O tamanho total dos arquivos baixados é 289 Mb. Para carregar esses arquivos para o armazenamento de blobs, crie o 'conjunto de dados' de contêiner do armazenamento de blobs em sua conta de armazenamento. É possível fazer isso acessando a página do Azure da sua conta de armazenamento, clicando em Blobs e em +Contêiner. Insira 'conjunto de dados' como Nome e clique em OK. As capturas de tela a seguir ilustram estas etapas:

![Abrir blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Abrir contêiner](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Depois disso, selecione o contêiner do conjunto de dados da lista e clique no botão Carregar. O Portal do Azure permite que você carregue vários arquivos simultaneamente. Na seção "Carregar blob", clique no botão da pasta, selecione todos os arquivos do conjunto de dados, clique em Abrir e, em seguida, clique em Carregar. A captura de tela a seguir ilustra essas etapas:

![Carregar blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

O upload dos arquivos leva vários minutos, dependendo da sua conexão com a Internet. 

Em nosso código, usamos o [SDK de Armazenamento do Microsoft Azure](https://azure-storage.readthedocs.io/en/latest/) para baixar o conjunto de dados do armazenamento de blobs para o ambiente de execução atual. O download é realizado em no arquivo load\_data() function from load_data.py. Para usar este código, é necessário substituir <ACCOUNT_NAME> e <ACCOUNT_KEY> pelo nome e pela chave primária de sua conta de armazenamento que hospeda o conjunto de dados. É possível visualizar o nome da conta no canto superior esquerdo da página do Azure da conta de armazenamento. Para obter a chave de conta, selecione Chaves de acesso na página de conta de armazenamento do Azure (consulte a primeira captura de tela na seção Ingestão de dados) e copie a cadeia de caracteres longa na primeira linha da coluna de chave:
 
![chave de acesso](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

O seguinte código da função load_data() baixa um único arquivo:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Observe que não é necessário executar o arquivo load_data.py manualmente. Posteriormente, será chamado de outros arquivos.

### <a name="feature-engineering"></a>Engenharia de recursos
O código para todos os recursos de computação está no arquivo feature\_engineering.py. Não é necessário executar o arquivo feature_engineering.py manualmente. Posteriormente, ele será chamado de outros arquivos.

Criamos vários conjuntos de recursos:
* Codificação one-hot da marca e do modelo do celular (função one\_hot\_brand_model)
* Fração dos eventos gerados pelo usuário em cada dia da semana (função weekday\_hour_features)
* Fração dos eventos gerados pelo usuário em cada hora (função weekday\_hour_features)
* Fração dos eventos gerados pelo usuário em cada combinação de dia da semana e hora (função weekday\_hour_features)
* Fração dos eventos gerados pelo usuário em cada aplicativo (função one\_hot\_app_labels)
* Fração dos eventos gerados pelo usuário em cada rótulo de aplicativo (função one\_hot\_app_labels)
* Fração dos eventos gerados pelo usuário em cada categoria de aplicativo (função text\_category_features)
* Recursos de indicador para categorias de aplicativos que foram usados para gerar eventos (função one\_hot_category)

Esses recursos foram inspirados pelo kernel do Kaggle [Um modelo linear em aplicativos e rótulos](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

A compilação desses recursos requer uma quantidade de memória significativa. Inicialmente, tentamos calcular recursos no ambiente local com 16 GB de RAM. Foi possível calcular os quatro primeiros conjuntos de recursos, mas recebemos o erro “Sem memória” ao calcular o quinto conjunto de recursos. O cálculo dos quatro primeiros conjuntos de recursos está no arquivo singleVMsmall.py e pode ser executado no ambiente local executando na 

     az ml experiment submit -c local .\singleVMsmall.py   

janela da CLI.

Como o ambiente local é muito pequeno para calcular todos os conjuntos de recursos, mudamos para o DSVM remoto que tem memória maior. A execução dentro do DSVM é feita dentro do contêiner do Docker gerenciado pelo AML Workbench. Usando este DSVM, é possível calcular todos os recursos e treinar modelos e ajustar parâmetros (consulte a próxima seção). arquivo singleVM.py tem a computação de recursos e código de modelagem completos. Na próxima seção, mostraremos como executar singleVM.py no DSVM remoto. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Ajustando hiperparâmetros usando o DSVM remoto
Usamos a implementação [xgboost](https://anaconda.org/conda-forge/xgboost) [1] do aumento da árvore de gradiente. Usamos o pacote [scikit-learn](http://scikit-learn.org/) para ajustar os hiperparâmetros de xgboost. Embora o xgboost não faça parte do pacote scikit-learn, ele implementa a API do scikit-learn e, portanto, pode ser usado juntamente com as funções de ajuste de parâmetro do scikit-learn. 

O xgboost tem oito hiperparâmetros:
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* descrição do objetivo A desses hiperparâmetros pode ser localizado em
- http://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn- https://github.com/dmlc/xgboost/blob/master/doc/parameter.md). 
- 
Inicialmente, utilizamos DSVM remoto e sintonizamos hiperparâmetros de uma pequena grade de valores candidatos:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Essa grade tem quatro combinações de valores de hiperparâmetros. Usamos a validação cruzada de 5 partições, resultando em 4x5=20 execuções do xgboost. Para medir o desempenho dos modelos, usamos a métrica de perda de log negativa. O código a seguir localiza os valores de hiperparâmetros na grade que maximizam a perda de log negativa de validação cruzada. O código também usa esses valores para treinar o modelo definitivo no conjunto completo de treinamento:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Depois de criar o modelo, salvamos os resultados do ajuste de hiperparâmetro. Usamos a API de log do AML Workbench para salvar os melhores valores de hiperparâmetros e da estimativa de validação cruzada correspondente da perda de log negativa:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Também criamos o arquivo sweeping_results.txt com perdas de log negativo e validado por cruzamento de todas as combinações de valores de hiperparâmetro na grade.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Esse arquivo é armazenado em um diretório ./de saídas especial. Posteriormente, mostraremos como baixá-lo.  

 Antes de executar singleVM.py na DSVM remota pela primeira vez, criamos um contêiner do Docker lá executando 

    az ml experiment prepare -c dsvm

nas janelas da CLI. A criação do contêiner do Docker leva vários minutos. Depois disso, executamos singleVM.py no DSVM:

    az ml experiment submit -c dsvm .\singleVM.py

Este comando é concluído em 1 hora e 38 minutos quando o DSVM tem 8 núcleos e 28 Gb de memória. Os valores registrados podem ser exibidos na janela Histórico de execuções do Workbench AML:

![histórico de execuções](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Por padrão, a janela Histórico de execuções mostra valores e grafos dos primeiros 1-2 valores registrados. Para ver a lista completa dos valores escolhidos de hiperparâmetros, clique no ícone de configurações marcado com círculo vermelho na captura de tela anterior. Em seguida, selecione os hiperparâmetros a serem mostrados na tabela. Além disso, para selecionar os grafos mostrados na parte superior da janela Histórico de execuções, clique no ícone de configuração marcado com um círculo azul e selecione os grafos na lista. 

Os valores de hiperparâmetros escolhidos também podem ser examinados na janela Propriedades da execução: 

![propriedades da execução](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

No canto superior direito da janela Propriedades de Execução, há uma seção Arquivos de Saída com a lista de todos os arquivos que foram criados na pasta '.\output'. sweeping\_results.txt pode ser baixado lá selecionando-o e clicando no botão Baixar. sweeping_results.txt deve ter a seguinte saída:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Ajustando hiperparâmetros usando o cluster Spark
Usamos o cluster Spark para aumentar o ajuste de hiperparâmetros e usar uma grade maior. Nossa nova grade é

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Essa grade tem 16 combinações de valores de hiperparâmetros. Como usamos a validação cruzada de 5 partições, executamos o xgboost 16x5=80 vezes.

O pacote scikit-learn não tem um suporte nativo de ajuste de hiperparâmetros usando o cluster Spark. Felizmente, o pacote [sklearn spark](https://spark-packages.org/package/databricks/spark-sklearn) dos Databricks preenche esta lacuna. Este pacote fornece a função GridSearchCV que tem quase a mesma API que a função GridSearchCV no scikit-learn. Para usar o spark-sklearn e ajustar os hiperparâmetros usando o Spark, é necessário se conectar para criar o contexto do Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Em seguida, substituímos 

    from sklearn.model_selection import GridSearchCV

por: 

    from spark_sklearn import GridSearchCV

Além disso, substituímos a chamada para GridSearchCV de scikit-learn para aquela em spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

O código definitivo para ajustar hiperparâmetros usando o Spark está no arquivo distributed\_sweep.py. A diferença entre singleVM.py e distributed_sweep.py está na definição da grade e nas quatro linhas de código adicionais. Observe também que, devido aos serviços do AML Workbench, o código do log não é alterado quando o ambiente de execução é alterado do DSVM remoto para o cluster Spark.

Antes de executar distributed_sweep.py no cluster Spark pela primeira vez, é necessário instalar os pacotes Python lá. Isso pode ser feito executando 

    az ml experiment prepare -c spark

nas janelas da CLI. Esta instalação leva vários minutos. Depois disso, executamos distributed_sweep.py no cluster Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

Esse comando é concluído em 1 hora 6 minutos quando o cluster Spark tem 6 nós de trabalho com 28 Gb de memória. Os resultados do ajuste fino de hiperparâmetros podem ser acessados no Azure Machine Learning Workbench da mesma forma que a execução DSVM remota. (ou seja, logs, melhores valores de hiperparâmetros e arquivo sweeping_results.txt)

### <a name="architecture-diagram"></a>Diagrama da arquitetura

O diagrama a seguir mostra o fluxo de trabalho geral: ![arquitetura](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Conclusão 

Nesse cenário, mostramos como usar o Azure Machine Learning Workbench para realizar ajustes finos de hiperparâmetros em máquinas virtuais remotas e clusters Spark. Vimos que o Azure Machine Learning Workbench fornece ferramentas para configuração fácil de ambientes de execução. Também permite comutação fácil entre eles. 

## <a name="references"></a>Referências

[1] T. Chen e C. Guestrin. [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754) (XGBoost: um sistema de aumento de árvore escalável). KDD 2016.




