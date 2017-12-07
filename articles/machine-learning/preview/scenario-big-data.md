---
title: "Previsão de carga de trabalho de servidor em terabytes de dados – Azure | Microsoft Docs"
description: Como treinar um modelo de machine learning sobre Big Data usando o Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: c7ed8e695097d0cf2f5c99f8ccf3378c4e553c3b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Previsão de carga de trabalho de servidor em terabytes de dados

Este artigo aborda como os cientistas de dados podem usar o Azure Machine Learning Workbench para desenvolver soluções que exigem o uso de Big Data. Você pode começar com um exemplo de um grande conjunto de dados, iterar pela preparação de dados, engenharia de recursos e machine learning e estender o processo para o todo o conjunto de dados. 

Você aprenderá sobre os principais recursos do Machine Learning Workbench abaixo:
* Facilidade de alternar entre os destinos de computação. Você pode configurar destinos de computação diferentes e usá-los em experimentação. Neste exemplo, você usa um DSVM Ubuntu e um cluster do Azure HDInsight como os destinos de computação. Você também pode configurar os destinos de computação dependendo da disponibilidade dos recursos. Particularmente, após expandir o cluster Spark com mais nós de trabalho, você pode usar os recursos por meio do Machine Learning Workbench para acelerar execuções de experimento.
* Rastreamento do histórico de execuções. Você pode usar o Machine Learning Workbench para rastrear o desempenho de modelos de machine learning e outras métricas de interesse.
* Operacionalização do modelo de machine learning. Você pode usar as ferramentas internas no Machine Learning Workbench para implantar o modelo de machine learning treinado como um serviço Web no Serviço de Contêiner do Azure. Você também pode usar o serviço Web para obter previsões de minilote por meio de chamadas à API REST. 
* Suporte a dados de terabytes.

> [!NOTE]
> Para obter exemplos de código e outros materiais relacionados a este exemplo, consulte o [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Visão geral de casos de uso

Prever a carga de trabalho nos servidores é uma necessidade de negócios comum para empresas de tecnologia que gerenciam suas próprias infraestruturas. Para reduzir os custos de infraestrutura, os serviços executados em servidores usados abaixo do esperado devem ser agrupados para execução em um número menor de máquinas. Os serviços executados em servidores com uso excessivo devem receber mais máquinas para execução. 

Neste cenário, você vai se concentrar na previsão de carga de trabalho para cada computador (ou servidor). Mais especificamente, você usa os dados da sessão em cada servidor para prever a classe de carga de trabalho do servidor no futuro. Você classifica a carga de cada servidor em classes baixa, média e alta usando o Classificador Random Forest no [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). As técnicas de aprendizado de máquina e o fluxo de trabalho neste exemplo podem ser facilmente estendidas para outros problemas semelhantes. 


## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para executar este exemplo são os seguintes:

* Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
* Uma cópia instalada do [Machine Learning Workbench](./overview-what-is-azure-ml.md). Para instalar o programa e criar um espaço de trabalho, consulte o [guia de instalação de início rápido](./quickstart-installation.md).
* Windows 10 (as instruções neste exemplo são geralmente as mesmas para sistemas macOS).
* Uma DSVM (Máquina Virtual de Ciência de Dados) para Linux (Ubuntu). É possível provisionar uma DSVM Ubuntu seguindo estas [instruções](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Você também pode ver [este guia de início rápido](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Recomendamos usar uma máquina virtual com pelo menos 8 núcleos e 32 GB de memória. Você precisa do endereço IP da DSVM, nome de usuário e senha para testar este exemplo. Salve a tabela a seguir com as informações da DSVM para etapas posteriores:

 Nome do campo| Valor |  
 |------------|------|
Endereço IP da DSVM | xxx|
 Nome de usuário  | xxx|
 Senha   | xxx|

 É possível optar por usar qualquer VM com o [Mecanismo do Docker](https://docs.docker.com/engine/) instalado.

* Um cluster Spark do HDInsight com Hortonworks Data Platform versão 3.6 e Spark versão 2.1. x. Visite [Criar um cluster Apache Spark no Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) para obter detalhes sobre como criar clusters HDInsight. Recomendamos usar um cluster de três trabalhos com cada trabalho tendo 16 núcleos e 112 GB de memória. Ou é possível escolher apenas o tipo de VM `D12 V2` para o nó de cabeçalho e `D14 V2` para o nó de trabalho. A implantação do cluster leva cerca de 20 minutos. Você precisa do nome do cluster, nome de usuário do SSH e senha para testar este exemplo. Salve a tabela a seguir com as informações do cluster do Azure HDInsight para etapas posteriores:

 Nome do campo| Valor |  
 |------------|------|
 Nome do cluster| xxx|
 Nome de usuário  | xxx (sshuser por padrão)|
 Senha   | xxx|


* Uma conta de armazenamento do Azure. Você pode seguir [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma. Além disso, crie dois contêineres de blob particulares com o nome `fullmodel` e `onemonthmodel` na conta de armazenamento. A conta de armazenamento é usada para salvar os resultados de computação intermediários e os modelos de aprendizado de máquina. Você precisa do nome da conta de armazenamento e da chave de acesso para testar este exemplo. Salve a tabela abaixo com as informações da conta de armazenamento do Azure para etapas posteriores:

 Nome do campo| Valor |  
 |------------|------|
 Nome da conta de armazenamento| xxx|
 Chave de acesso  | xxx|


A DSVM Ubuntu e o cluster do Azure HDInsight criados na lista de pré-requisitos são destinos de computação. Os destinos de computação são o recurso de computação no contexto do Machine Learning Workbench, que pode ser diferente do computador no qual o Workbench é executado.   

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Machine Learning Workbench.
2.  Na página **Projetos**, selecione o sinal **+** e selecione **Novo Projeto**.
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto.
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite **Previsão da carga de trabalho sobre dados de terabytes** e selecione o modelo.
5.  Selecione **Criar**.

É possível criar um projeto do Workbench com um repositório git pré-criado seguindo esta [instrução](./tutorial-classifying-iris-part-1.md).  
Execute `git status` para inspecionar o status dos arquivos para controle de versão.

## <a name="data-description"></a>Descrição dos dados

Os dados usados neste exemplo são dados de carga de trabalho de servidor sintetizados. Eles estão hospedados em uma conta de armazenamento de Blobs do Azure publicamente acessível. As informações da conta de armazenamento específicas podem ser encontradas no campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). É possível usar os dados diretamente do armazenamento de blobs. Se o armazenamento é usado por muitos usuários simultaneamente, é possível usar [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) para baixar os dados para seu próprio armazenamento. 

O tamanho total dos dados é de aproximadamente 1 TB. Cada arquivo tem cerca de 1 a 3 GB e está no formato de arquivo CSV sem cabeçalho. Cada linha de dados representa a carga de uma transação em um servidor específico. As informações detalhadas do esquema de dados são as seguintes:

Número da coluna | Nome do campo| Tipo | Descrição |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datetime |    Hora de início da sessão
2  |`SessionEnd`    | Datetime | Hora de término da sessão
3 |`ConcurrentConnectionCounts` | Número inteiro | Número de conexões simultâneas
4 | `MbytesTransferred` | Duplo | Dados normalizados transferidos em megabytes
5 | `ServiceGrade` | Número inteiro |  Nível de serviço da sessão
6 | `HTTP1` | Número inteiro|  Sessão usa HTTP1 ou HTTP2
7 |`ServerType` | Número inteiro   |Tipo de servidor
8 |`SubService_1_Load` | Duplo |   Carga do subserviço 1
9 | `SubService_1_Load` | Duplo |  Carga do subserviço 2
10 | `SubService_1_Load` | Duplo |     Carga do subserviço 3
11 |`SubService_1_Load` | Duplo |  Carga do subserviço 4
12 | `SubService_1_Load`| Duplo |      Carga do subserviço 5
13 |`SecureBytes_Load`  | Duplo | Carga de bytes seguros
14 |`TotalLoad` | Duplo | Carga total no servidor
15 |`ClientIP` | Cadeia de caracteres|    Endereço IP do cliente
16 |`ServerIP` | Cadeia de caracteres|    Endereço IP do servidor



Observe que os tipos de dados esperados estão listados na tabela anterior. Devido a problemas de dados sujos e valores ausentes, não há nenhuma garantia de que os tipos de dados estão realmente conforme o esperado. O processamento de dados deve levar isso em consideração. 


## <a name="scenario-structure"></a>Estrutura do cenário

Os arquivos neste exemplo são organizados da seguinte maneira.

| Nome do arquivo | Tipo | Descrição |
|-----------|------|-------------|
| `Code` | Pasta | A pasta contém todo o código no exemplo. |
| `Config` | Pasta | A pasta contém os arquivos de configuração. |
| `Image` | Pasta | A pasta usada para salvar as imagens para o arquivo LEIAME. |
| `Model` | Pasta | A pasta usada para salvar arquivos de modelo baixados do armazenamento de Blobs. |
| `Code/etl.py` | Arquivo Python | O arquivo Python usado para a preparação de dados e engenharia de recursos. |
| `Code/train.py` | Arquivo Python | O arquivo Python usado para treinar um modelo multiclassificação de três classes.  |
| `Code/webservice.py` | Arquivo Python | O arquivo Python usado para operacionalização.  |
| `Code/scoring_webservice.py` | Arquivo Python |  O arquivo Python usado para a transformação de dados e para chamar o serviço Web. |
| `Code/O16Npreprocessing.py` | Arquivo Python | O arquivo Python usado para pré-processar os dados para scoring_webservice.py.  |
| `Code/util.py` | Arquivo Python | O arquivo Python que contém o código para ler e gravar blobs do Azure.  
| `Config/storageconfig.json` | Arquivo JSON | O arquivo de configuração para o contêiner de blobs do Azure que armazena os resultados intermediários e o modelo para processar e treinar em dados de um mês. |
| `Config/fulldata_storageconfig.json` | Arquivo Json | O arquivo de configuração para o contêiner de blobs do Azure que armazena os resultados intermediários e o modelo para processar e treinar em um conjunto de dados completo.|
| `Config/webservice.json` | Arquivo JSON | O arquivo de configuração para scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Arquivo YAML | O arquivo de dependência Conda. |
| `Config/conda_dependencies_webservice.yml` | Arquivo YAML | O arquivo de dependência Conda para serviço Web.|
| `Config/dsvm_spark_dependencies.yml` | Arquivo YAML | O arquivo de dependência Spark para DSVM Ubuntu. |
| `Config/hdi_spark_dependencies.yml` | Arquivo YAML | O arquivo de dependência Spark para o cluster Spark do HDInsight. |
| `README.md` | Arquivo markdown | O arquivo markdown LEIAME. |
| `Code/download_model.py` | Arquivo Python | O arquivo Python usado para baixar os arquivos de modelo do blob do Azure para o disco local. |
| `Docs/DownloadModelsFromBlob.md` | Arquivo markdown | O arquivo markdown que contém instruções sobre como executar `Code/download_model.py`. |



### <a name="data-flow"></a>Fluxo de dados

O código no [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carrega dados do contêiner publicamente acessível (campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Ele inclui a preparação de dados e a engenharia de recursos e salva os resultados e modelos de computação intermediários em seu próprio contêiner privado. O código no [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carrega os resultados de computação intermediários do contêiner privado, treina o modelo de classificação multiclasse e grava o modelo de aprendizado de máquina treinado no contêiner privado. 

Você deve usar um contêiner para experimentação no conjunto de dados de um mês e outro para experimentação no conjunto de dados completo. Como os dados e os modelos são salvos como arquivo Parquet, cada arquivo é realmente uma pasta no contêiner que contém vários blobs. O contêiner resultante tem a seguinte aparência:

| Nome do prefixo do blob | Tipo | Descrição |
|-----------|------|-------------|
| featureScaleModel | Parquet | Modelo do scaler padrão para recursos numéricos. |
| stringIndexModel | Parquet | Modelo do indexador de cadeia de caracteres para recursos não numéricos.|
| oneHotEncoderModel|Parquet | Modelo do codificador one-hot para recursos categóricos. |
| mlModel | Parquet | Modelo de aprendizado de máquina treinado. |
| informações| Arquivo pickle Python | As informações sobre os dados transformados, incluindo o início do treinamento, término do treinamento, duração, o carimbo de data/hora para a divisão de teste-treinamento e colunas para indexação e codificação one-hot.

Todos os arquivos e blobs na tabela anterior são usados para operacionalização.


### <a name="model-development"></a>Desenvolvimento do modelo

#### <a name="architecture-diagram"></a>Diagrama da arquitetura


O diagrama a seguir mostra o fluxo de trabalho de ponta a ponta do uso do Machine Learning Workbench para desenvolver o modelo: ![arquitetura](media/scenario-big-data/architecture.PNG)

Nas seções a seguir, mostramos o desenvolvimento do modelo usando a funcionalidade de destino de computação remota no Machine Learning Workbench. Primeiro, carregamos alguns dados de exemplo pequenos e executamos o script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) em uma DSVM Ubuntu para rápida iteração. É possível limitar ainda mais o trabalho que fazemos no [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) passando um argumento extra para iteração mais rápida. No final, usamos um cluster do HDInsight para treinar usando dados completos.     

O arquivo [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carrega e prepara os dados e executa a engenharia de recursos. Ele aceita dois argumentos:
* Um arquivo de configuração para o contêiner de armazenamento de blobs para armazenar os modelos e resultados intermediários de computação.
* Um argumento de configuração de depuração para iteração mais rápida.

O primeiro argumento, `configFilename`, é um arquivo de configuração local em que você armazena as informações de armazenamento de blobs e especifica para onde carregar os dados. Por padrão, é o [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) e será usado na execução de dados de um mês. Também incluímos o [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), de que você precisa para usar na execução do conjunto de dados completo. O conteúdo na configuração é o seguinte: 

| Campo | Tipo | Descrição |
|-----------|------|-------------|
| storageAccount | Cadeia de caracteres | Nome da conta de armazenamento do Microsoft Azure |
| storageContainer | Cadeia de caracteres | Contêiner na conta de armazenamento do Microsoft Azure para armazenar resultados intermediários |
| storageKey | Cadeia de caracteres |Chave de acesso da conta de armazenamento do Microsoft Azure |
| dataFile|Cadeia de caracteres | Arquivos de fonte de dados  |
| duration| Cadeia de caracteres | Duração dos dados nos arquivos de fonte de dados|

Modifique `Config/storageconfig.json` e `Config/fulldata_storageconfig.json` para configurar a conta de armazenamento, a chave de armazenamento e o contêiner de blobs para armazenar os resultados intermediários. Por padrão, o contêiner de blobs para a execução de dados de um mês é `onemonthmodel` e o contêiner de blobs para a execução do conjunto de dados completo é `fullmodel`. Certifique-se de criar esses dois contêineres na conta de armazenamento. O campo `dataFile` em [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configura quais dados são carregados em [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). O campo `duration` configura o intervalo incluído nos dados. Se a duração for definida como ONE_MONTH, os dados carregados deverão ser apenas um arquivo csv entre os sete arquivos dos dados para junho de 2016. Se a duração for FULL, o conjunto de dados completo (1 TB) será carregado. Não é necessário alterar `dataFile` e `duration` nesses dois arquivos de configuração.

O segundo argumento é DEBUG. Defini-lo como FILTER_IP permite uma iteração mais rápida. O uso desse parâmetro é útil quando você deseja depurar o script.

> [!NOTE]
> Em todos os comandos a seguir, substitua as variáveis de argumento por seus valores reais.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Desenvolvimento de modelo no Docker da DSVM Ubuntu

#####  <a name="1-set-up-the-compute-target"></a>1. Configurar o destino de computação

Comece a linha de comando do Machine Learning Workbench selecionando **Arquivo** > **Abrir Prompt de Comando**. Em seguida, execute: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Os dois arquivos abaixo são criados na pasta aml_config do projeto:

-  dockerdsvm.compute: o arquivo contém as informações de conexão e configuração de um destino de execução remota.
-  dockerdsvm.runconfig: o arquivo é um conjunto de opções de execução usado dentro do aplicativo Workbench.

Navegue até dockerdsvm.runconfig e altere a configuração desses campos da seguinte forma:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Prepare o ambiente de projeto executando:

```az ml experiment prepare -c dockerdsvm```


Com `PrepareEnvironment` definido como true, o Machine Learning Workbench cria o ambiente de tempo de execução sempre que você enviar um trabalho. `Config/conda_dependencies.yml` e `Config/dsvm_spark_dependencies.yml` contêm a personalização do ambiente de tempo de execução. É sempre possível modificar as dependências Conda, a configuração e as dependências do Spark editando esses dois arquivos YMAL. Neste exemplo, adicionamos `azure-storage` e `azure-ml-api-sdk` como pacotes Python extra no `Config/conda_dependencies.yml`. Também adicionamos `spark.default.parallelism`, `spark.executor.instances`, e `spark.executor.cores` a `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparação de dados e engenharia de recursos no Docker DSVM

Execute o script `etl.py` no Docker DSVM. Use um parâmetro de depuração que filtra os dados carregados com endereços IP de servidor específico:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Navegue até o painel lateral e selecione **Executar** para ver o histórico de execução de `etl.py`. Observe que o tempo de execução é de aproximadamente dois minutos. Se você planejar alterar seu código para incluir novos recursos, fornecer FILTER_IP como o segundo argumento propiciará uma iteração mais rápida. Talvez seja necessário executar esta etapa várias vezes ao lidar com seu próprios problemas de aprendizado de máquina para explorar o conjunto de dados ou criar novos recursos. 

Com a restrição personalizada sobre quais dados devem ser carregados e a filtragem adicional de quais dados devem ser processados, é possível acelerar o processo de iteração em seu desenvolvimento de modelo. Ao testar, você deve salvar periodicamente as alterações em seu código no repositório Git. Observe que usamos o código a seguir no `etl.py` para permitir o acesso ao contêiner privado:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Em seguida, execute o script `etl.py` no Docker DSVM sem o parâmetro de depuração FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Navegue até o painel lateral e selecione **Executar** para ver o histórico de execução de `etl.py`. Observe que o tempo de execução é de cerca de quatro minutos. O resultado processado desta etapa é salvo no contêiner e carregado para treinamento em train.py. Além disso, os indexadores de cadeias de caracteres, os pipelines de codificador e os scalers padrão são salvos no contêiner privado. Eles são usados na operacionalização. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Treinamento de modelo no Docker DSVM

Execute o script `train.py` no Docker DSVM:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Esta etapa carrega os resultados de computação intermediários da execução de `etl.py` e treina um modelo de aprendizado de máquina. Esta etapa leva cerca de dois minutos.

Quando você tiver concluído com êxito a experimentação nos dados pequenos, será possível continuar executando a experimentação no conjunto de dados completo. Você pode começar usando o mesmo código e experimentar com as alterações no destino de computação e do argumento.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Desenvolvimento do modelo no cluster do HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Criação do destino de computação no Machine Learning Workbench para o cluster HDInsight

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Os dois arquivos abaixo são criados na pasta aml_config:
    
-  myhdo.compute: o arquivo contém informações de conexão e configuração de um destino de execução remota.
-  myhdi.runconfig: o arquivo é um conjunto de opções de execução usado dentro do aplicativo Workbench.


Navegue até myhdi.runconfig e altere a configuração desses campos da seguinte forma:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Prepare o ambiente de projeto executando:

```az ml experiment prepare -c myhdi```

Esta etapa pode levar até sete minutos.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparação de dados e engenharia de recursos no cluster do HDInsight

Execute o script `etl.py` com fulldata no cluster do HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Como este trabalho tem uma duração relativamente longa (aproximadamente duas horas), você pode usar `-a` para desabilitar o fluxo de saída. Quando o trabalho é feito, em **Histórico de execução**, você pode exibir os logs de driver e controlador. Se você tiver um cluster maior, sempre será possível reconfigurar as configurações no `Config/hdi_spark_dependencies.yml` para usar mais instâncias ou núcleos. Por exemplo, se você tiver um cluster de quatro nós de trabalho, será possível aumentar o valor do `spark.executor.instances` de 5 para 7. É possível ver a saída dessa etapa no contêiner **fullmodel** em sua conta de armazenamento. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Treinamento de modelo no cluster do HDInsight

Execute o script `train.py` no cluster do HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Como este trabalho tem uma duração relativamente longa (aproximadamente 30 minutos), você pode usar `-a` para desabilitar o fluxo de saída.

#### <a name="run-history-exploration"></a>Exploração do histórico de execuções

O histórico de execuções é um recurso que permite o acompanhamento de sua experimentação no Machine Learning Workbench. Por padrão, ele controla a duração da experimentação. Em nosso exemplo específico, quando migramos para o conjunto de dados completo para `Code/etl.py` na experimentação, observamos que a duração aumenta consideravelmente. Também é possível registrar métricas específicas para fins de acompanhamento. Para habilitar o acompanhamento de métricas, adicione as seguintes linhas de código ao cabeçalho do seu arquivo Python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Veja um exemplo para acompanhar uma métrica específica:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Na barra lateral direita do Workbench, navegue até **Execuções** para ver o histórico de execuções para cada arquivo Python. Você também pode ir para seu repositório do GitHub. Um novo branch com o nome começando com “AMLHistory” é criado para acompanhar a alteração realizada em seu script em cada execução. 


### <a name="operationalize-the-model"></a>Operacionalizar o modelo

Nesta seção, você vai operacionalizar o modelo criado nas etapas anteriores como um serviço Web. Você também aprenderá a usar o serviço Web para prever a carga de trabalho. Use CLIs (interfaces de linha de comando) de operacionalização de Machine Language para empacotar o código e as dependências como imagens do Docker e publicar o modelo como serviço Web em contêineres. Para obter mais informações, confira [esta visão geral](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md).

Você pode usar o prompt de linha de comando no Machine Learning Workbench para executar as CLIs.  Você também pode executar as CLIs no Ubuntu Linux seguindo o [guia de instalação](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Em todos os comandos a seguir, substitua as variáveis de argumento por seus valores reais. Leva cerca de 40 minutos para concluir esta seção.
> 

Escolha uma cadeia de caracteres exclusiva como o ambiente para operacionalização. Aqui, usaremos a cadeia de caracteres "[unique]" para representar a cadeia de caracteres que você escolher.

1. Criar o ambiente para operacionalização e o grupo de recursos.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Observe que você pode usar o Serviço de Contêiner do Azure como o ambiente usando o `--cluster` no comando `az ml env setup`. Você pode operacionalizar o modelo de machine learning no [Serviço de Contêiner do Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Ele usa [Kubernetes](https://kubernetes.io/) para automatizar a implantação, o dimensionamento e o gerenciamento de aplicativos em contêineres. Esse comando leva cerca de 20 minutos para ser executado. Use o seguinte para verificar se a implantação foi concluída com êxito: 

        az ml env show -g [unique]rg -n [unique]

   Defina o ambiente de implantação como o que você acabou de criar executando o seguinte:

        az ml env set -g [unique]rg -n [unique]

2. Crie e use uma conta de gerenciamento de modelos. Para criar uma conta de gerenciamento de modelos, execute o seguinte:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Use o gerenciamento de modelos para operacionalização executando o seguinte:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Uma conta de gerenciamento de modelos é usada para gerenciar os modelos e os serviços Web. No portal do Azure, você pode ver que uma nova conta de gerenciamento de modelo foi criada. Você pode ver modelos, manifestos, imagens do Docker e serviços que são criados usando essa conta de gerenciamento de modelo.

3. Baixe e registre os modelos.

   Baixe os modelos no contêiner **fullmodel** para seu computador local no diretório do código. Não baixe o arquivo de dados parquet com o nome "vmlSource.parquet". Não é um arquivo de modelo; é um resultado intermediário de computação. Também é possível reutilizar os arquivos de modelo incluídos no repositório Git. Para saber mais, consulte o [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Vá para a pasta `Model` na CLI e registre os modelos da seguinte forma:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   A saída de cada comando fornece uma ID de modelo necessária na próxima etapa. Salve-os em um arquivo de texto para uso futuro.

4. Crie um manifesto para o serviço Web.

   Um manifesto inclui o código para o serviço Web, todos os modelos de aprendizado de máquina e as dependências do ambiente de tempo de execução. Acesse a pasta `Code` na CLI e execute o seguinte comando:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   A saída fornece uma ID de manifesto para a próxima etapa. 

   Permaneça no diretório `Code` e você poderá testar webservice.py executando o seguinte: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Crie uma imagem do Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   A saída fornece uma ID de imagem para a próxima etapa, pois essa imagem do Docker é usada no Serviço de Contêiner. 

6. Implante o serviço Web no cluster do Serviço de Contêiner.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   A saída fornece uma ID de serviço. Você precisa usá-lo para obter a chave de autorização e a URL do serviço.

7. Chame o serviço Web no código Python para pontuar em minilotes.

   Use o seguinte comando para obter a chave de autorização:

         az ml service keys realtime -i $ServiceID 

   Use o seguinte comando para obter a URL de pontuação do serviço:

        az ml service usage realtime -i $ServiceID

   Modifique o conteúdo em `./Config/webservice.json` com a URL de pontuação de serviço e a chave de autorização certas. Mantenha o "portador" no arquivo original e substitua a parte "xxx". 
   
   Vá para o diretório raiz do seu projeto e teste o serviço Web para a pontuação do minilote usando o seguinte:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Dimensione o serviço Web. 

   Para saber mais, confira [Como dimensionar a operacionalização no Cluster do Serviço de Contêiner do Azure](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md).
 

## <a name="next-steps"></a>Próximas etapas

Este exemplo destaca como usar o Machine Learning Workbench para treinar um modelo de aprendizado de máquina sobre Big Data e operacionalizar o modelo treinado. Mais especificamente, você aprendeu a configurar e usar destinos de computação diferentes, executar o histórico das métricas de controle e usar execuções diferentes.

Você pode estender o código para explorar a validação cruzada e o ajuste de hiperparâmetro. Para saber mais sobre a validação cruzada e o ajuste de hiperparâmetro, confira [este recurso do GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Para saber mais sobre a previsão de série temporal, confira [este recurso do GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
