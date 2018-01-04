---
title: "Criar um modelo para os serviços do Azure Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Azure Machine Learning (versão prévia) de ponta a ponta. Essa é a parte dois e aborda a experimentação."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/06/2017
ms.openlocfilehash: f3b4b41593e0956e98f05c7f8d1c71632a489e56
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="classify-iris-part-2-build-a-model"></a>Parte 2 de Classificação da Íris: Criar um modelo
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a parte dois de uma série de três partes. Nessa parte do tutorial, você usará os serviços do Azure Machine Learning (versão prévia) para:

> [!div class="checklist"]
> * Usar o Azure Machine Learning Workbench.
> * Abrir os scripts e examinar o código.
> * Executar scripts em um ambiente local.
> * Consultar o histórico de execução.
> * Executar scripts em um ambiente do Docker local.
> * Executar scripts em uma janela de CLI do Azure local.
> * Executar scripts em um ambiente do Docker remoto.
> * Executar scripts em um ambiente do Azure HDInsight na nuvem.

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. As capturas de tela são específicas do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>pré-requisitos
Concluir a primeira parte desta série de tutoriais. Siga o [tutorial Preparar dados](tutorial-classifying-iris-part-1.md) para criar recursos do Azure Machine Learning e instalar o aplicativo Azure Machine Learning Workbench antes de iniciar as etapas neste tutorial.

Opcionalmente, você pode fazer experiências com a execução de scripts em um contêiner do Docker local. Para fazer isso, você precisa de um mecanismo Docker (o Community Edition é suficiente) instalado e iniciado localmente no seu computador Windows ou Mac OS. Para obter mais informações sobre como instalar o Docker, confira [Instruções de instalação do Docker](https://docs.docker.com/engine/installation/).

Para fazer experiências com o script de expedição a ser executado em um contêiner do Docker em uma VM do Azure remota, ou em um cluster Spark do Azure HDInsight, siga as [instruções para criar uma Máquina Virtual de Ciência de Dados do Azure ou um Cluster HDInsight baseado em Ubuntu](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Examine os arquivos de configuração e iris_sklearn.py
1. Abra o aplicativo Azure Machine Learning Workbench e abra o projeto **myIris** criado na parte anterior da série de tutorial.

2. Após abrir o projeto, selecione o botão **Arquivos** (o ícone de pasta) no painel mais à esquerda para abrir a ista de arquivos na sua pasta de projetos.

3. Selecione o arquivo **iris_sklearn.py**. O código Python abre em uma nova guia do editor de texto dentro do workbench.

   ![Abra o arquivo](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >O código que você vê pode não ser exatamente o mesmo que o código anterior, já que este projeto de exemplo é atualizado com frequência.

4. Examine o código de script de Python para se familiarizar com o estilo de codificação. O script executa as seguintes tarefas:

   - Carrega o pacote de preparação de dados **iris.dprep** para criar um [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Use o pacote de preparação de dados `iris.dprep` que é fornecido com o projeto de exemplo, que deve ser o mesmo que o arquivo `iris-1.dprep` criado na parte 1 deste tutorial.

   - Adiciona recursos aleatórios para tornar o problema mais difícil de resolver. A aleatoriedade é necessária porque o Íris é um pequeno conjunto de dados que pode ser facilmente classificado com quase 100% de precisão.

   - Usa a biblioteca de aprendizado de máquina [scikit-learn](http://scikit-learn.org/stable/index.html) para criar um modelo de regressão logística. 

   - Serializa o modelo inserindo a biblioteca [pickle](https://docs.python.org/2/library/pickle.html) em um arquivo na pasta `outputs`. Em seguida, o script carrega e desserializa a pasta de volta na memória.

   - Usa o modelo desserializado para fazer a previsão em um novo registro. 

   - Plota dois grafos, uma matriz de confusão e uma curva característica de operação (CCO) do receptor multiclasse, usando a biblioteca [matplotlib](https://matplotlib.org/) e, em seguida, salva-os na pasta `outputs`.

   - O objeto `run_logger` é usado ao longo do processo para registrar a taxa de regularização e para precisão do modelo nos logs. Os logs são automaticamente plotados no histórico de execuções.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Executar o script iris_sklearn.py em um ambiente local

Nos prepararemos para executar o script **iris_sklearn.py** pela primeira vez. Este script requer os pacotes **scikit-learn** e **matplotlib**. O pacote **scikit-learn** já está instalado pelo Azure Machine Learning Workbench. Você precisa instalar o **matplotlib** mesmo assim. 

1. No Azure Machine Learning Workbench, selecione o menu **Arquivo** e, em seguida, selecione **Abrir o prompt de comando** para abrir o prompt de comando. A janela de interface de linha de comando é chamada de *janela de CLI do Azure Machine Learning Workbench* ou, abreviando, *janela de CLI*.

2. Na janela de CLI, digite o seguinte comando para instalar o pacote **matplotlib** do Python. Isso deve ser concluído em menos de um minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Se você ignorou o comando `pip install` anterior, o código em `iris_sklearn.py` é executado com sucesso. Se você executar somente `iris_sklearn.py`, o código não produz os gráficos da saída de matriz de confusão e da curva CCO multiclasse conforme mostrado nas visualizações de histórico.

3. Retorne para a janela de aplicativo do workbench. 

4. Na barra de ferramentas na parte superior da guia **iris_sklearn.py**, selecione para abrir o menu suspenso ao lado do ícone **Salvar** e, em seguida, selecione **Executar configuração**. Selecione **local** como o ambiente de execução e, em seguida, insira `iris_sklearn.py` como o script a executar.

5. Em seguida, vá para o lado direito da barra de ferramentas e insira `0.01` no campo **Argumentos**. 

   ![Controle de execução](media/tutorial-classifying-iris/run_control.png)

6. Selecione o botão **Executar**. Um trabalho é agendado imediatamente. O trabalho é listado no painel **Trabalhos** no lado direito da janela do workbench. 

7. Após alguns instantes, o status do trabalho transiciona de **Enviando** para **Executando** e, em seguida, para **Concluído**.

   ![Executar sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Selecione **Concluído** no texto de status do trabalho no painel **Trabalhos**. Uma janela pop-up é aberta e exibe o texto de saída padrão (stdout) do script em execução. Para fechar o texto stdout, selecione o botão **Fechar** (**x**) no canto superior direito da janela pop-up.

9. No mesmo status de trabalho no painel **Trabalhos**, selecione o texto azul **iris_sklearn.py [n]** (_n_ é o número da execução) imediatamente acima do status **Concluído** e da hora de início. A janela **Propriedades de Execução** é aberta e mostra as seguintes informações para aquela execução específica:
   - Informações sobre **Propriedades da Execução**
   - Arquivos de **Saídas**
   - **Visualizações**, se houver
   - **Logs** 

   Quando a execução é concluída, a janela pop-up mostra os seguintes resultados:

   >[!NOTE]
   >Já que introduzimos algum caráter aleatório ao conjunto de treinamento definido anteriormente, os seus resultados exatos podem ser diferentes dos resultados mostrados aqui.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```

10. Feche a guia **Propriedades da Execução** e retorne para a guia **iris_sklearn.py**. 

11. Repita as execuções adicionais. 

    Insira uma série de valores numéricos diferentes no campo **Argumentos** desde `0.001` até `10`. Clique em **Executar** para executar o código mais algumas vezes. O valor do argumento que você alterar a cada vez será alimentado no algoritmo de regressão logística no código, e isso provoca resultados diferentes a cada vez.

## <a name="review-the-run-history-in-detail"></a>Examinar o histórico de execuções com detalhes
No Azure Machine Learning Workbench, cada execução do script é capturada como um registro do histórico de execuções. Se você abrir o modo de **Execuções**, você pode exibir o histórico de execuções de um script específico.

1. Para abrir a lista de **Execuções**, selecione o botão **Execuções** (ícone de relógio) na barra de ferramentas à esquerda. Em seguida, selecione **iris_sklearn.py** para mostrar o **Painel de Execução** de `iris_sklearn.py`.

   ![Modo de execução](media/tutorial-classifying-iris/run_view.png)

2. A guia **Painel de Execução** se abrirá. Examine as estatísticas capturadas em várias execuções. Os grafo são renderizados na parte superior da guia. Cada execução tem um número consecutivo, e os detalhes de execução são listados na tabela na parte inferior da tela.

   ![Painel de execução](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtre a tabela e, em seguida, selecione qualquer um dos grafos para exibir o status, a duração, a precisão e a taxa de regularização de cada execução. 

4. Selecione duas ou três execuções na tabela **Execuções** e, em seguida, selecione o botão **Comparar** para abrir uma página de comparação detalhada. Examine a comparação lado a lado. Selecione o botão voltar da **Lista de execuções** no canto superior esquerdo do painel **Comparação** para retornar para o **Painel de Execução**.

5. Selecione uma execução individual para ver a exibição de detalhes de execução. Observe que as estatísticas para a execução selecionada são listadas na seção **Propriedades da Execução**. Os arquivos gravados na pasta de saída são listados na seção **Saídas** e você pode baixar os arquivos nesta seção.

   ![Detalhes da execução](media/tutorial-classifying-iris/run_details.png)

   Os dois gráficos, a matriz de confusão e a curva CCO multiclasse são renderizados na seção **Visualizações**. Todos os arquivos de log também podem ser encontrados na seção **Logs**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Executar scripts no ambiente do Docker local

Com o Machine Learning, você pode configurar facilmente ambientes de execução adicionais como o Docker e executar o script nesses ambientes. 

>[!IMPORTANT]
>Para realizar esta etapa, você deve ter o mecanismo do Docker localmente instalado e iniciado. Para obter mais informações, consulte as instruções de instalação do Docker.

1. No painel à esquerda, selecione o ícone de **Pasta** para abrir a lista de **Arquivos** para o seu projeto. Expanda a pasta `aml_config`. 

2. Há vários ambientes pré-configurados como **docker-python**, **docker-spark** e **local**. 

   Cada ambiente possui dois arquivos, como `docker-python.compute` e `docker-python.runconfig`. Abra cada arquivo para ver que algumas opções são configuráveis no editor de texto.  

   Para limpar, selecione **Fechar** (**x**) nas guias para quaisquer editores de texto abertos.

3. Execute o script **iris_sklearn.py** usando o ambiente **docker-python**: 

   - Na barra de ferramentas à esquerda, selecione o ícone de **Relógio** para abrir o painel **Execuções**. Selecione **Todas as execuções**. 
   - Na parte superior da guia **Todas as Execuções**, selecione **docker-python** como o ambiente de destino em vez do **local** padrão. 
   - Em seguida, vá para o lado direito e selecione **iris_sklearn.py** como o script a executar. 
   - Deixe o campo **Argumentos** em branco, pois o script especifica um valor padrão. 
   - Selecione o botão **Executar**.

4. Observe que um novo trabalho é iniciado. Ele é listado no painel **Trabalhos** no lado direito da janela do workbench.

   Quando você executa no Docker pela primeira vez, ele leva mais alguns minutos extra para ser concluído. 

   Nos bastidores, o Azure Machine Learning Workbench cria um novo arquivo do Docker. 
   O novo arquivo faz referência à imagem base do Docker especificada no arquivo `docker.compute` e os pacotes do Python de dependência especificados no arquivo `conda_dependencies.yml`. 
   
   O mecanismo do Docker realiza as seguintes tarefas:

    - Baixa a imagem base do Azure.
    - Instala os pacotes do Python especificados no arquivo `conda_dependencies.yml`.
    - Inicia um contêiner do Docker.
    - Copia ou referencia, dependendo da configuração de execução, a cópia local da pasta do projeto.      
    - Executa o script `iris_sklearn.py`.

   No final, você deve ver exatamente o mesmo resultado que veria ao usar **local** como destino.

5. Agora experimentaremos o Spark. A imagem base do Docker contém uma instância do Spark previamente instalada e configurada. Por causa dessa instância, você pode executar um script PySpark nela. Essa é uma maneira fácil para desenvolver e testar seu programa Spark sem precisar gastar tempo para instalar e configurar o Spark por conta própria. 

   Abra o arquivo `iris_spark.py` . Este script carrega o arquivo de dados `iris.csv` e usa o algoritmo de regressão logística da biblioteca de Machine Learning do Spark para classificar o conjunto de dados Íris. Agora, altere o ambiente de execução para **docker-spark** e o script para **iris_spark.py** e, em seguida, execute novamente. Esse process leva um pouco mais de tempo, já que uma sessão do Spark deve ser criada e iniciada dentro do contêiner do Docker. Você também pode ver que o stdout é diferente de stdout do `iris_spark.py`.

6. Realize mais algumas execuções e experimente argumentos diferentes. 

7. Abra o arquivo `iris_spark.py` para ver o modelo de regressão logística criado usando a biblioteca de Machine Learning do Spark. 

8. Interaja com o painel **Trabalhos**, execute uma exibição de lista de histórico e execute uma exibição de detalhes das suas execuções em diferentes ambientes de execução.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Executar scripts na janela de CLI do Azure Machine Learning

1. No Azure Machine Learning Workbench, abra a janela de linha de comando, selecione o menu **Arquivo** e, em seguida, selecione **Abrir o prompt de comando**. O seu prompt de comando inicia na pasta do projeto com o prompt `C:\Temp\myIris\>`.

   >[!IMPORTANT]
   >Você deve usar a janela de linha de comando (aberta por meio do workbench) para realizar as etapas a seguir.

2. Use o prompt de comando para fazer logon no Azure. 

   O aplicativo workbench e a CLI usam caches de credenciais independentes ao autenticar para recursos do Azure. Você só precisa fazer isso uma vez, até que o token em cache expire. O comando **az account list** retorna a lista de assinaturas disponíveis para seu logon. Se houver mais de um, use o valor da ID da assinatura desejada. Defina essa assinatura como a conta padrão para usar com o comando **az account set -s** e, em seguida, forneça o valor da ID da assinatura. Em seguida, confirme a configuração usando o comando account **show**.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Depois que a autenticação for concluída e o contexto atual da assinatura do Azure estiver definido, digite os comandos a seguir na janela de CLI para instalar a **matplotlib** e enviar o script Python como um experimento a ser executado.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Examine a saída. Você tem a mesma saída e resultados que tinha quando usou o workbench para executar o script. 

5. Execute o mesmo script novamente usando o ambiente de execução do Docker se você tiver o Docker instalado em seu computador.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. No workbench, selecione ícone **Pasta** no painel à esquerda para listar os arquivos de projeto e abra o script Python chamado **run.py**. 

   Esse script é útil para executar um loop sobre várias taxas de regularização. Execute o experimento várias vezes com essas taxas. Esse script inicia um trabalho `iris_sklearn.py` com uma taxa de regularização de `10.0` (um número absurdamente grande). O script, em seguida, corta a taxa pela metade na execução seguinte, e assim por diante, até que a taxa não fique menor do que `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Para abrir o script **run.py** a partir da linha de comando, execute os seguintes comandos:

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Quando `run.py` for concluído, você verá um grafo em sua exibição de lista de histórico de execuções no workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Execute em um contêiner de Docker em um computador remoto
Para executar o script em um contêiner do Docker em um computador Linux remoto, você precisa ter acesso SSH (nome de usuário e senha) para esse computador remoto. Além disso, o computador remoto deve ter o mecanismo do Docker instalado e em execução. A maneira mais fácil de obter um computador Linux desse tipo é criando uma Máquina Virtual de Ciência de Dados (DSVM) baseada no Ubuntu no Azure. Saiba [como criar uma DSVM do Ubuntu para usar no Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>*Não* há suporte para DSVM com base em CentOS.

1. Depois que a VM é criada, você pode anexar a VM como um ambiente de execução, gerando um par de arquivos `.runconfig` e `.compute`. Execute o comando a seguir para gerar a chave. Nomearemos o novo ambiente `myvm`.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >A área de endereço IP também pode ser um nome de domínio totalmente qualificado (FQDN) publicamente endereçável, tal como `vm-name.southcentralus.cloudapp.azure.com`. É uma boa prática adicionar o FQDN à sua DSVM e usá-lo aqui, em vez de um endereço IP. Essa prática é uma boa ideia de como você pode desativar a VM em algum momento para economizar custos. Além disso, na próxima vez que você iniciar a VM, o endereço IP poderá ter sido alterado.

   Em seguida, execute o comando a seguir no constructo na imagem do Docker na VM para prepará-la para executar os scripts:
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Você também pode alterar o valor de `PrepareEnvironment` na `myvm.runconfig` do `false` padrão para `true`. Isso altera automaticamente o contêiner de Docker na primeira execução.

2. Edite o arquivo `myvm.runconfig` gerado sob `aml_config` e altere a estrutura do `PySpark` padrão para `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Se você deixar a configuração do framework como PySpark, isso também deve funcionar. No entanto, isso é menos eficiente se você não precisar realmente que uma sessão do Spark execute o script Python.

3. Envie o mesmo comando que você enviou na janela da CLI, mas desta vez use _myvm_ como destino:
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   O comando é executado como se você estivesse em um ambiente `docker-python`, exceto pelo fato de que a execução ocorre na VM Linux remota. A janela CLI exibe as mesmas informações de saída.

4. Vamos tentar usar o Spark no contêiner. Abra o Explorador de Arquivos. Você também pode fazer isso na janela de CLI se estiver familiarizado com os comandos básicos de manipulação de arquivos. Faça uma cópia do arquivo `myvm.runconfig` e nomeie-o `myvm-spark.runconfig`. Edite o novo arquivo para alterar a configuração `Framework` de `Python` para `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Não faça nenhuma alteração ao arquivo `myvm.compute`. A mesma imagem do Docker na mesma VM é usada para a execução do Spark. No novo `myvm-spark.runconfig`, o campo `target` aponta para o mesmo arquivo `myvm.compute` por meio de seu nome `myvm`.

5. Digite o comando a seguir para executá-lo na instância do Spark no contêiner do Docker remoto:
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Execute o script em um cluster HDInsight
Você também pode executar esse script em um cluster HDInsight Spark. Saiba [como criar o cluster HDInsight Spark para usar no Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>![OBSERVAÇÃO] O cluster HDInsight deve usar o Blob do Azure como o armazenamento primário. O uso do armazenamento do Azure Data Lake ainda não tem suporte.

1. Se você tiver acesso a um cluster Spark para o Azure HDInsight, gere um comando de configuração de execução HDInsight, conforme mostrado aqui. Forneça o nome do cluster HDInsight, seu nome de usuário HDInsight e a senha como parâmetros. Use o seguinte comando:

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   O nó principal do cluster FQDN normalmente é `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >O `username` é o nome de usuário SSH do cluster. O valor padrão é `sshuser` se você não alterá-lo durante o provisionamento do HDInsight. O valor não é `admin`, que é o outro usuário criado durante a configuração para habilitar o acesso ao site de administração do cluster. 

2. Execute o comando a seguir e o script será executado no cluster HDInsight:

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Quando você executar em um cluster HDInsight remoto, você também poderá exibir os detalhes do Outro Negociador de Recursos (YARN) em `https://<cluster_name>.azurehdinsight.net/yarnui` usando a conta de usuário `admin`.


## <a name="next-steps"></a>Próximas etapas
Nessa segunda parte da série de tutoriais de três partes, você aprendeu a usar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Usar o Azure Machine Learning Workbench.
> * Abrir os scripts e examinar o código.
> * Executar scripts em um ambiente local.
> * Consultar o histórico de execução.
> * Executar scripts em um ambiente do Docker local.
> * Executar scripts em uma janela de CLI do Azure local.
> * Executar scripts em um ambiente do Docker remoto.
> * Executar scripts em um ambiente do HDInsight na nuvem.

Você está pronto para passar para a terceira parte da série. Agora que você criou o modelo de regressão logística, nós o implantaremos como um serviço Web em tempo real.

> [!div class="nextstepaction"]
> [Implantar um modelo](tutorial-classifying-iris-part-3.md)
