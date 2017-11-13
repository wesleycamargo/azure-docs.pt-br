---
title: "Implantar um modelo para os serviços do Azure Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Azure Machine Learning (versão prévia) de ponta a ponta. Essa é a parte três e discute o modelo de implantação."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 11/2/2017
ms.openlocfilehash: b6cdd135d2d264c8b4ede1592c686cdeea3d0a59
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="classify-iris-part-3-deploy-a-model"></a>Parte 3 de Classificação da Íris: Implantar um modelo
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais. Os cientistas de dados podem usá-los para preparar dados, desenvolver experimentos e implantar modelos em escala de nuvem.

Este tutorial é a terceira e última parte de uma série. Nessa parte do tutorial, você usará os serviços do Azure Machine Learning (versão prévia) para:

> [!div class="checklist"]
> * Localizar o arquivo de modelo.
> * Gerar um script de pontuação e um arquivo de esquema.
> * Preparar o ambiente.
> * Criar um serviço Web em tempo real.
> * Executar o serviço Web em tempo real.
> * Examinar os dados de blob de saída. 

 Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/iris_flower_data_set) atemporal. As capturas de tela são específicas do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Conclua as primeiras duas partes desta série de tutoriais:

   * Siga o [tutorial Preparar dados](tutorial-classifying-iris-part-1.md) para criar recursos do Machine Learning e instalar o aplicativo Azure Machine Learning Workbench.

   * Siga o [tutorial Criar um modelo](tutorial-classifying-iris-part-2.md) para criar um modelo de regressão logística no Azure Machine Learning.

Você precisa de um mecanismo do Docker instalado e em execução localmente. Como alternativa, você pode implantar em um cluster do Serviço de Contêiner do Azure no Azure.

## <a name="download-the-model-pickle-file"></a>Baixe o arquivo de pickle do modelo
Na parte anterior do tutorial, o script **iris_sklearn.py** foi executado localmente no Machine Learning Workbench. Essa ação serializou o modelo de regressão logística usando o popular pacote de serialização de objeto do Python [pickle](https://docs.python.org/2/library/pickle.html). 

1. Abra o aplicativo Machine Learning Workbench e, em seguida, abra o projeto **myIris** criado na parte anterior da série de tutoriais.

2. Após abrir o projeto, selecione o botão **Arquivos** (ícone de pasta) no painel à esquerda para abrir a ista de arquivos na sua pasta de projetos.

3. Selecione o arquivo **iris_sklearn.py**. O código Python abre em uma nova guia do editor de texto dentro do workbench.

4. Examine o arquivo **iris_sklearn.py** para ver onde o arquivo pickle foi gerado. Selecione Control + F para abrir a caixa de diálogo **Localizar** e, em seguida, localize a palavra **pickle** no código Python.

   Este trecho de código mostra como o arquivo de saída processado pelo pickle foi gerado. O arquivo pickle de saída é denominado **model.pkl** no disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Localize o arquivo de modelo pickle nos arquivos de saída de uma execução anterior.
   
   Quando você executou o script **iris_sklearn.py**, o arquivo de modelo foi gravado na pasta **outputs** com o nome **model.pkl**. Essa pasta existe no ambiente de execução em que você opta por executar o script e não na pasta local do projeto. 
   
   - Para localizar o arquivo, selecione o botão **Execuções** (ícone de relógio) na barra de ferramentas para abrir a lista de **Todas as Execuções**.  
   - A guia **Todas as Execuções** será aberta. Na tabela de execuções, selecione uma das execuções recentes em que o destino era **local** e o nome do script era **iris_sklearn.py**. 
   - O painel **Propriedades da Execução** será aberto. No canto superior direito do painel, observe a seção **Saídas**. 
   - Baixe o arquivo pickle, selecionando a caixa de seleção ao lado do arquivo **model.pkl** e, em seguida, selecione o botão **Baixar**. Salve-o na raiz da pasta do projeto. O arquivo é necessário nas etapas posteriores.

   ![Baixe o arquivo de pickle](media/tutorial-classifying-iris/download_model.png)

   Leia mais sobre a pasta `outputs` no artigo [Como ler e gravar arquivos de dados grandes](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Gerar um script de pontuação e arquivos de esquema
Para implantar o serviço Web você precisará, além do arquivo de modelo, também de um script de pontuação e, opcionalmente, de um esquema para os dados de entrada do serviço Web. O script de pontuação carrega o arquivo **model.pkl** da pasta atual e usa-o para gerar uma classe Íris recentemente prevista.  

1. Abra o aplicativo Azure Machine Learning Workbench e, em seguida, abra o projeto **myIris** criado na parte anterior da série de tutoriais.

2. Após abrir o projeto, selecione o botão **Arquivos** (ícone de pasta) no painel à esquerda para abrir a ista de arquivos na sua pasta de projetos.

3. Selecione o arquivo **iris_score.py**. O script de Python é aberto. Esse arquivo é usado como o arquivo de pontuação.

   ![Arquivo de pontuação](media/tutorial-classifying-iris/model_data_collection.png)

4. Para obter o arquivo de esquema, execute o script. Selecione o ambiente **local** e o script **iris_score.py** na barra de comandos e, em seguida, clique no botão **Executar**. 

5. Esse script cria um arquivo JSON na seção **Saídas**, que captura o esquema de dados de entrada exigido pelo modelo.

6. Observe o painel **Trabalhos** no lado direito do **Painel do Projeto**. Aguarde que a versão mais recente do trabalho **score_iris.py** exiba o status verde **Concluído**. Em seguida, selecione o hiperlink **score_iris.py [1]** da execução de trabalho mais recente para ver os detalhes da execução de **score_iris.py**. 

7. Na página **Propriedades de Execução**, na seção **Saídas**, selecione o arquivo recém-criado **service_schema.json**.  Marque a caixa de seleção ao lado do nome de arquivo e, em seguida, selecione **Baixar**. Salve o arquivo na pasta raiz do projeto.

8. Retorne à guia anterior onde você abriu o script **score_iris.py**. Ao usar a coleta de dados, é possível capturar previsões e entradas de modelo do serviço Web. As etapas a seguir são de especial interesse para a coleta de dados.

9. Examine o código na parte superior da classe de importações de arquivo **ModelDataCollector**, pois ela contém a funcionalidade de coleta de dados de modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Examine as seguintes linhas de código na função **init()**, que instancia **ModelDataCollector**:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Examine as seguintes linhas de código na função **run(input_df)**, já que ela coleta dados de entrada e de previsão:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Agora você está pronto para preparar o ambiente para operacionalizar o modelo.

>[!NOTE]
>Implantar modelos exige que você tenha acesso de proprietário a uma assinatura do Azure.

## <a name="prepare-to-operationalize-locally"></a>Preparar para operacionalizar localmente
Use a implantação de _modo local_ para executar em contêineres do Docker no computador local.

Você pode usar o _modo local_ para desenvolvimento e teste. O mecanismo do Docker deve ser executado localmente para concluir as etapas a seguir para operacionalizar o modelo. Você pode usar o sinalizador `-h` no final dos comandos para obter ajuda sobre o comando.

>[!NOTE]
>Se você não tem o mecanismo do Docker localmente, você ainda pode prosseguir criando um cluster no Azure para implantação. Apenas certifique-se de excluir o cluster após o tutorial, para que você não incorra em encargos contínuos.

1. Abra a interface de linha de comando (CLI) do Azure.
   No aplicativo Azure Machine Learning Workbench, n menu **Arquivo**, selecione **Abrir o prompt de comando**.

   O prompt de linha de comando é aberto em sua localização atual da pasta do projeto **c:\temp\myIris>**.

2. Verifique se o provedor de recursos do Azure **Microsoft.ContainerRegistry** está registrado em sua assinatura. Você deve registrar este provedor de recursos antes de criar um ambiente na etapa 3. Você pode verificar se ele já está registrado usando o seguinte comando:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Você deve ver uma saída como a abaixo: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Se o **Microsoft.ContainerRegistry** não estiver registrado, você pode registrá-lo usando o seguinte comando:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   O registro pode levar alguns minutos. Você pode verificar seu status usando o comando **az provider list** anterior ou o comando a seguir:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A terceira linha da saída exibe **"registrationState": "Registrar"**. Aguarde alguns minutos e repita o comando **show** até que a saída exiba **"registrationState": "Registrado"**.

3. Crie o ambiente. Você deve executar essa etapa uma vez para cada ambiente. Por exemplo, executá-la uma vez para o ambiente de desenvolvimento e uma vez para produção. Use o _modo local_ para esse primeiro ambiente. Você pode tentar usar a opção `-c` ou `--cluster` no comando a seguir para configurar um ambiente no _modo de cluster_ posteriormente:

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga as instruções na tela para provisionar uma conta de armazenamento para armazenar imagens do Docker, um registro de contêiner do Azure que lista as imagens do Docker e uma conta de AppInsight para coletar telemetria. Se você usar a opção `-c`, ele também criará um cluster do Serviço de Contêiner do Azure.
   
   O nome do cluster é uma maneira de identificar o ambiente. O local deve ser o mesmo que o local da conta de Gerenciamento de Modelos criada no portal do Azure.

4. Criar uma conta de Gerenciamento de Modelos. (Essa é uma configuração que só precisa ser realizada uma vez).  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Configurar a conta de Gerenciamento de Modelos.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Defina o ambiente.

   Depois que a configuração for concluída, use o comando a seguir para definir as variáveis de ambiente necessárias para operacionalizar o ambiente. Use o mesmo nome de ambiente que você usou anteriormente na etapa 4. Use o mesmo nome de grupo de recursos que foi enviado na janela de comando quando o processo de configuração foi concluído.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. Para verificar se você configurou corretamente seu ambiente operacionalizado para a implantação de serviço Web local, digite o seguinte comando:

   ```azurecli
   az ml env show
   ```

Agora você está pronto para criar o serviço Web em tempo real.

>[!NOTE]
>Você pode reutilizar sua conta de Gerenciamento de Modelos e o ambiente para implantações de serviço Web posteriores. Você não precisa criá-los para cada serviço Web. Uma conta ou um ambiente pode ter vários serviços Web associados a ele.

## <a name="create-a-real-time-web-service-in-one-command"></a>Criar um serviço Web em tempo real em um comando
1. Use o comando a seguir para criar um serviço Web em tempo real:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Esse comando gera uma ID de serviço Web que você pode usar mais tarde.

   As opções a seguir são usadas com o comando **az ml service create realtime**:
   * `-n`: o nome do aplicativo, que deve ter só letras minúsculas.
   * `-f`: o nome do arquivo de script de pontuação.
   * `--model-file`: o arquivo de modelo. Nesse caso, é o arquivo pickled model.pkl.
   * `-r`: o tipo de modelo. Nesse caso, é um modelo de Python.
   * `--collect-model-data true`: isso habilita a coleta de dados.

   >[!IMPORTANT]
   >O nome do serviço, que também é o novo nome de imagem do Docker, deverá ser composto somente de letras minúsculas. Caso contrário, você obterá um erro. 

2. Quando você executar o comando, o modelo e o arquivo de pontuação serão carregados para a conta de armazenamento que você criou como parte da configuração do ambiente. O processo de implantação cria uma imagem do Docker com o modelo, o esquema e o arquivo de pontuação e envia-o por push para o registro de contêiner do Azure: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   O comando efetua pull dessa imagem localmente para o seu computador e inicia um contêiner do Docker com base nessa imagem. Se seu ambiente estiver configurado no modo de cluster, o contêiner do Docker será implantado no cluster Kubernetes dos Serviços de Nuvem do Azure em vez disso.

   Como parte da implantação, um ponto de extremidade REST HTTP para o serviço Web é criado no computador local. Depois de alguns minutos, o comando deverá terminar com uma mensagem de êxito e o serviço Web estará pronto para ser usado.

3. Você pode ver o contêiner do Docker em execução usando o comando **docker ps**:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Criar um serviço Web em tempo real usando comandos separados
Como uma alternativa para o comando **az ml service create realtime** mostrado anteriormente, você também pode executar as etapas separadamente. 

Primeiro, registre o modelo. Em seguida, gere o manifesto, compile a imagem do Docker e crie o serviço Web. Essa abordagem passo a passo fornece mais flexibilidade em cada etapa. Além disso, você pode reutilizar entidades geradas na etapa anterior e recompilar entidades apenas quando necessário.

1. Registre o modelo, fornecendo o nome do arquivo pickle.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Este comando gera uma ID de modelo.

2. Criar um manifesto.

   Para criar um manifesto, use o comando a seguir e forneça a saída de ID de modelo da etapa anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Este comando gera uma ID de manifesto.

3. Crie uma imagem do Docker.

   Para criar uma imagem do Docker, use o comando a seguir e forneça a saída do valor da ID de manifesto da etapa anterior:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Este comando gera uma ID de imagem do Docker.
   
4. Criar o serviço.

   Para criar um serviço, use o comando a seguir e forneça a saída de ID de imagem da etapa anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Este comando gera uma ID de serviço Web.

Agora você está pronto para executar o serviço Web.

## <a name="run-the-real-time-web-service"></a>Executar o serviço Web em tempo real

Teste o serviço Web **irisapp** em execução, use um registro codificado por JSON contendo uma matriz de quatro números aleatórios:

1. O serviço Web inclui dados de exemplo. Ao executar em modo local, você pode chamar o comando **az ml service show realtime**. Essa chamada recupera um exemplo de comando de execução útil usar durante o teste do serviço. A chamada também recupera a URL de pontuação que você poderá usar para incorporar o serviço em seu próprio aplicativo personalizado:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Para testar o serviço, execute o comando de execução de serviço retornado:

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   A saída é **"2",**, que é a classe prevista. (O seu resultado pode ser diferente.) 

3. Se você quiser executar o serviço de fora da CLI, você precisará obter as chaves para autenticação:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Exiba os dados coletados no Armazenamento de Blobs do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize todas as suas contas de armazenamento. Para isso, selecione **Mais Serviços**.

3. Na caixa de pesquisa, insira **Contas de armazenamento** e, em seguida, selecione a tecla **Enter**.

4. Na caixa de pesquisa **Contas de armazenamento**, selecione o recurso de **Conta de armazenamento** correspondente ao seu ambiente. 

   > [!TIP]
   > Para determinar qual conta de armazenamento está em uso:
   > 1. Abra o Azure Machine Learning Workbench.
   > 2. Selecione o projeto no qual você está trabalhando.
   > 3. Abra um prompt de linha de comando no menu **Arquivo**.
   > 4. No prompt da linha de comando, insira `az ml env show -v` e verifique o valor *storage_account*. Esse é o nome da sua conta de armazenamento.

5. Após o painel da **Conta de armazenamento** abrir, selecione **Contêineres** na lista à esquerda. Localize o contêiner nomeado **modeldata**. 
 
   Se você não vir todos os dados, você precisará aguardar até 10 minutos após a primeira solicitação de serviço Web para ver a propagação dos dados para a conta de armazenamento.

   Os dados fluem para blobs com o seguinte caminho de contêiner:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Você pode consumir esses dados no armazenamento de Blobs do Azure. Há uma variedade de ferramentas que usam tanto o software da Microsoft quanto ferramentas de código-fonte aberto, como:

   - Azure Machine Learning: abra o arquivo CSV, adicionando o arquivo CSV como uma fonte de dados. 
   - Excel: abra os arquivos CSV diários como uma planilha.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): crie gráficos contendo dados extraídos por pull de dados CSV em blobs.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): carregue dados CSV em uma tabela de Hive e execute consultas SQL diretamente nos blobs.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): crie um dataframe com uma grande porção de dados CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Próximas etapas
Nessa terceira parte da série de tutoriais de três partes, você aprendeu a usar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Localizar o arquivo de modelo.
> * Gerar um script de pontuação e um arquivo de esquema.
> * Preparar o ambiente.
> * Criar um serviço Web em tempo real.
> * Executar o serviço Web em tempo real.
> * Examinar os dados de blob de saída. 

Você executou com êxito um script de treinamento em vários ambientes de computação, criou um modelo, serializou esse modelo e operacionalizou-o por meio de um serviço Web baseado em Docker. 

Você agora está pronto para realizar preparação de dados avançada:
> [!div class="nextstepaction"]
> [Preparação de dados avançada](tutorial-bikeshare-dataprep.md)
