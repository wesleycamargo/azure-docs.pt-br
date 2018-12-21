---
title: Criar um tutorial de modelo para o serviço do Azure Machine Learning (versão prévia) | Microsoft Docs
description: Este tutorial completo mostra como usar o serviço do Azure Machine Learning (versão prévia) de ponta a ponta. Essa é a parte dois e aborda a experimentação.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 03/15/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 2e4ede4a699eaac1e5f8c632901239fb04ebcd30
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276750"
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Tutorial 2: Classificar Iris – criar um modelo

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

O serviço do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a **parte dois de uma série de três partes**. Nessa parte do tutorial, você usará o serviço do Azure Machine Learning para:

> [!div class="checklist"]
> * Abrir os scripts e examinar o código
> * Executar scripts em um ambiente local
> * Examinar os históricos de execuções
> * Executar scripts em uma janela de CLI do Azure local
> * Executar scripts em um ambiente do Docker local
> * Executar scripts em um ambiente do Docker remoto
> * Executar scripts em um ambiente do Azure HDInsight na nuvem

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:
- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
- Uma conta de experimentação e o Azure Machine Learning Workbench instalado, conforme descrito neste [início rápido](quickstart-installation.md)
- O projeto e dados preparados do Iris da [Parte 1 do tutorial](tutorial-classifying-iris-part-1.md)
- Um mecanismo do Docker instalado e em execução localmente. Docker Community Edition é suficiente. Saiba como instalar o Docker aqui: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Examine os arquivos de configuração e iris_sklearn.py

1. Inicialize o aplicativo Azure Machine Learning Workbench.

1. Abra o projeto **myIris** criado na [Parte 1 da série de tutoriais](tutorial-classifying-iris-part-1.md).

2. No projeto aberto, selecione o botão **Arquivos** (o ícone de pasta) no painel mais à esquerda para abrir a ista de arquivos na sua pasta de projetos.

   ![Abra o projeto do Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Selecione o arquivo de script **iris_sklearn.py** do Python. 

   ![Escolher um script](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   O código abre em uma nova guia do editor de texto dentro do Workbench. Este é o script que você usará em toda esta parte do tutorial. 

   >[!NOTE]
   >O código que você vê pode não ser exatamente o mesmo que o código anterior, já que este projeto de exemplo é atualizado com frequência.
   
   ![Abra o arquivo](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Inspecione o código de script do Python para se familiarizar com o estilo de codificação. 

   O script **iris_sklearn.py** executa as seguintes tarefas:

   * Carrega o pacote de preparação de dados padrão chamado **iris.dprep** para criar um [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Adiciona recursos aleatórios para tornar o problema mais difícil de resolver. A aleatoriedade é necessária porque o Iris é um pequeno conjunto de dados que pode ser facilmente classificado com quase 100% de precisão.

   * Usa a biblioteca de aprendizado de máquina [scikit-learn](http://scikit-learn.org/stable/index.html) para criar um modelo de regressão logística.  Essa biblioteca é fornecida com o Azure Machine Learning Workbench por padrão.

   * Serializa o modelo usando a biblioteca [pickle](https://docs.python.org/3/library/pickle.html) em um arquivo na pasta `outputs`. 
   
   * Carrega o modelo serializado e, em seguida, desserializa-o de volta na memória.

   * Usa o modelo desserializado para fazer a previsão em um novo registro. 

   * Plota dois grafos, uma matriz de confusão e uma curva característica de operação (CCO) do receptor multiclasse usando a biblioteca [matplotlib](https://matplotlib.org/) e as salva na pasta `outputs`. É possível instalar essa biblioteca em seu ambiente caso ela ainda não esteja nele.

   * Plota a taxa de regularização e a precisão do modelo no histórico de execução automaticamente. O objeto `run_logger` é usado ao longo do processo para registrar a taxa de regularização e para precisão do modelo nos logs. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Executar iris_sklearn.py em seu ambiente local

1. Inicie a interface de linha de comando (CLI) do Azure Machine Learning:
   1. Inicialize o Azure Machine Learning Workbench.

   1. No menu de Workbench, selecione **Arquivo** > **Abrir Prompt de Comando**. 
   
   A janela da interface de linha de comando (CLI) do Azure Machine Learning inicia na pasta do projeto `C:\Temp\myIris\>` no Windows. Esse projeto é o mesmo que você criou na Parte 1 do tutorial.

   >[!IMPORTANT]
   >Você deve usar essa janela da CLI para realizar as próximas etapas.

1. Na janela da CLI, instale a biblioteca de plotagem do Python, **matplotlib**, caso ainda não a tenha.

   O script **iris_sklearn.py** tem dependências em dois pacotes do Python: **scikit-learn** e **matplotlib**.  O pacote **scikit-learn** já está instalado pelo Azure Machine Learning Workbench para sua conveniência. Mas você precisa instalar o **matplotlib** caso ainda não o tenha.

   Se você seguir em frente sem instalar o **matplotlib**, o código deste tutorial ainda pode ser executado com êxito. Contudo, o código não conseguirá produzir os gráficos da saída de matriz de confusão e da curva CCO multiclasse mostrados nas visualizações de histórico.

   ```azurecli
   pip install matplotlib
   ```

   A instalação leva cerca de um minuto.

1. Retorne para a janela do aplicativo Workbench. 

1. Localize a guia chamada **iris_sklearn.py**. 

   ![Localize a guia com script](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. Na barra de ferramentas da guia, selecione **local** como o ambiente de execução e `iris_sklearn.py` como o script a ser executado. Eles já podem estar selecionados.

   ![Local e a opção de script](media/tutorial-classifying-iris/2-local-script.png)

1. Vá para o lado direito da barra de ferramentas e insira `0.01` no campo **Argumentos**. 

   Esse valor corresponde à taxa de regularização do modelo de regressão logística.

   ![Local e a opção de script](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Selecione o botão **Executar**. Um trabalho é agendado imediatamente. O trabalho é listado no painel **Trabalhos** no lado direito da janela do workbench. 

   ![Local e a opção de script](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Após alguns instantes, o status do trabalho muda de **Enviando** para **Executando** e, por fim, para **Concluído**.

1. Selecione **Concluído** no texto de status do trabalho no painel **Trabalhos**. 

   ![Executar sklearn](media/tutorial-classifying-iris/2-completed.png)

   Uma janela pop-up é aberta e exibe o texto de saída padrão (stdout) para a execução. Para fechar o texto stdout, selecione o botão **Fechar** (**x**) no canto superior direito da janela pop-up.

   ![Saída padrão](media/tutorial-classifying-iris/2-standard-output.png)

9. No mesmo status de trabalho no painel **Trabalhos**, selecione o texto azul **iris_sklearn.py [n]** (_n_ é o número da execução) imediatamente acima do status **Concluído** e da hora de início. A janela **Propriedades de Execução** é aberta e mostra as seguintes informações para aquela execução específica:
   - Informações sobre **Propriedades da Execução**
   - **Saídas**
   - **Métricas**
   - **Visualizações**, se houver
   - **Logs** 

   Quando a execução é concluída, a janela pop-up mostra os seguintes resultados:

   >[!NOTE]
   >Como o tutorial introduz um caráter aleatório ao conjunto de treinamento definido anteriormente, os seus resultados exatos podem ser diferentes dos resultados mostrados aqui.

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

11. Repita para execuções adicionais. 

    Insira uma série de valores diferentes no campo **Argumentos**, desde `0.001` até `10`. Clique em **Executar** para executar o código mais algumas vezes. O valor do argumento que você alterar a cada vez será alimentado no modelo de regressão logística no código, resultando em descobertas diferentes a cada vez.

## <a name="review-the-run-history-in-detail"></a>Examinar o histórico de execuções com detalhes
No Azure Machine Learning Workbench, cada execução do script é capturada como um registro do histórico de execuções. Se você abrir o modo de **Execuções**, você pode exibir o histórico de execuções de um script específico.

1. Para abrir a lista de **Execuções**, selecione o botão **Execuções** (ícone de relógio) na barra de ferramentas à esquerda. Em seguida, selecione **iris_sklearn.py** para mostrar o **Painel de Execução** de `iris_sklearn.py`.

   ![Modo de execução](media/tutorial-classifying-iris/run_view.png)

1. A guia **Painel de Execução** se abrirá. 

   Examine as estatísticas capturadas em várias execuções. Os grafo são renderizados na parte superior da guia. Cada execução tem um número consecutivo, e os detalhes de execução são listados na tabela na parte inferior da tela.

   ![Painel de execução](media/tutorial-classifying-iris/run_dashboard.png)

1. Filtre a tabela e, em seguida, selecione qualquer um dos grafos para exibir o status, a duração, a precisão e a taxa de regularização de cada execução. 

1. Marque as caixas de seleção ao lado de duas ou mais execuções na tabela **Execuções**. Selecione o botão **Comparar** para abrir um painel de comparação detalhada. Examine a comparação lado a lado. 

1. Para voltar ao **Painel de Execução**, selecione o botão de voltar da **Lista de Execuções**, no canto superior esquerdo do painel **Comparação**.

   ![Retornar à Lista de Execuções](media/tutorial-classifying-iris/2-compare-back.png)

1. Selecione uma execução individual para ver a exibição de detalhes de execução. Observe que as estatísticas para a execução selecionada são listadas na seção **Propriedades da Execução**. Os arquivos gravados na pasta de saída são listados na seção **Saídas** e você pode baixar os arquivos nesta seção.

   ![Detalhes da execução](media/tutorial-classifying-iris/run_details.png)

   Os dois gráficos, a matriz de confusão e a curva CCO multiclasse são renderizados na seção **Visualizações**. Todos os arquivos de log também podem ser encontrados na seção **Logs**.


## <a name="run-scripts-in-local-docker-environments"></a>Executar scripts em ambientes do Docker local

Opcionalmente, você pode fazer experiências com a execução de scripts em um contêiner do Docker local. É possível configurar ambientes de execução adicionais, como o Docker, e executar o script nesses ambientes. 

>[!NOTE]
>Para fazer experiências com o script de expedição a ser executado em um contêiner do Docker em uma VM do Azure remota, ou em um cluster Spark do Azure HDInsight, siga as [instruções para criar uma Máquina Virtual de Ciência de Dados do Azure ou um Cluster HDInsight baseado em Ubuntu](how-to-create-dsvm-hdi.md).

1. Caso ainda não tenha feito isso, instale e inicie o Docker localmente em seu computador Windows ou MacOS. Para obter mais informações, consulte as instruções de instalação do Docker em https://docs.docker.com/install/. A Community Edition é suficiente.

1. No painel à esquerda, selecione o ícone de **Pasta** para abrir a lista de **Arquivos** para o seu projeto. Expanda a pasta `aml_config`. 

2. Há vários ambientes pré-configurados: **docker-python**, **docker-spark** e **local**. 

   Cada ambiente tem dois arquivos, como `docker.compute` (para **docker python** e **docker spark**) e `docker-python.runconfig`. Abra cada arquivo para ver que algumas opções são configuráveis no editor de texto.  

   Para limpar, selecione **Fechar** (**x**) nas guias para quaisquer editores de texto abertos.

3. Execute o script **iris_sklearn.py** usando o ambiente **docker-python**: 

   - Na barra de ferramentas à esquerda, selecione o ícone de **Relógio** para abrir o painel **Execuções**. Selecione **Todas as execuções**. 

   - Na parte superior da guia **Todas as Execuções**, selecione **docker-python** como o ambiente de destino em vez do **local** padrão. 

   - Em seguida, vá para o lado direito e selecione **iris_sklearn.py** como o script a executar. 

   - Deixe o campo **Argumentos** em branco, pois o script especifica um valor padrão. 

   - Selecione o botão **Executar**.

4. Observe que um novo trabalho é iniciado. Ele é listado no painel **Trabalhos** no lado direito da janela do workbench.

   Ao executar no Docker pela primeira vez, o trabalho leva alguns minutos a mais para ser concluído. 

   Nos bastidores, o Azure Machine Learning Workbench cria um novo arquivo do Docker. 
   O novo arquivo faz referência à imagem base do Docker especificada no arquivo `docker.compute` e os pacotes do Python de dependência especificados no arquivo `conda_dependencies.yml`. 
   
   O mecanismo do Docker realiza as seguintes tarefas:

    - Baixa a imagem base do Azure.
    - Instala os pacotes do Python especificados no arquivo `conda_dependencies.yml`.
    - Inicia um contêiner do Docker.
    - Copia ou referencia, dependendo da configuração de execução, a cópia local da pasta do projeto.      
    - Executa o script `iris_sklearn.py`.

   No final, você deve ver exatamente os mesmos resultados que veria ao usar **local** como destino.

5. Agora experimentaremos o Spark. A imagem base do Docker contém uma instância Spark já instalada e configurada que você pode usar para executar um script PySpark. Usar essa imagem base é uma maneira fácil de desenvolver e testar seu programa Spark sem precisar gastar tempo instalando e configurando o Spark por conta própria. 

   Abra o arquivo `iris_spark.py` . Este script carrega o arquivo de dados `iris.csv` e usa o algoritmo de regressão logística da biblioteca de Machine Learning do Spark para classificar o conjunto de dados Íris. Agora, altere o ambiente de execução para **docker-spark** e o script para **iris_spark.py** e, em seguida, execute novamente. Esse process leva um pouco mais de tempo, já que uma sessão do Spark deve ser criada e iniciada dentro do contêiner do Docker. Você também pode ver que o stdout é diferente de stdout do `iris_spark.py`.

6. Inicie mais algumas execuções e experimente argumentos diferentes. 

7. Abra o arquivo `iris_spark.py` para ver o modelo de regressão logística criado usando a biblioteca de Machine Learning do Spark. 

8. Interaja com o painel **Trabalhos**, execute uma exibição de lista de histórico e execute uma exibição de detalhes das suas execuções em diferentes ambientes de execução.

## <a name="run-scripts-in-the-cli-window"></a>Executar scripts na janela da CLI

1. Inicie a interface de linha de comando (CLI) do Azure Machine Learning:
   1. Inicialize o Azure Machine Learning Workbench.

   1. No menu de Workbench, selecione **Arquivo** > **Abrir Prompt de Comando**. 
   
   O prompt da CLI é iniciado na pasta de projeto `C:\Temp\myIris\>` no Windows. Esse é o projeto criado na Parte 1 da série de tutoriais.

   >[!IMPORTANT]
   >Você deve usar essa janela da CLI para realizar as próximas etapas.

1. Na janela da CLI, faça logon no Azure. [Saiba mais sobre o az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   É possível que você já esteja conectado. Nesse caso, pode ignorar esta etapa.

   1. No prompt de comando, insira:
      ```azurecli
      az login
      ```

      Esse comando retorna um código para usar em seu navegador em https://aka.ms/devicelogin.

   1. Vá para https://aka.ms/devicelogin em seu navegador.

   1. Quando for solicitado, insira no seu navegador o código recebido na CLI.

   O aplicativo Workbench e a CLI usam caches de credenciais independentes ao autenticar para recursos do Azure. Depois de entrar, você não precisará autenticar novamente até que o token em cache expire. 

1. Se sua organização tiver várias assinaturas do Azure (ambiente corporativo), você deve definir a assinatura a ser usada. Localize sua assinatura, configure-a usando a ID da assinatura e teste-a.

   1. Liste todas as assinaturas do Azure às quais você tem acesso usando este comando:
   
      ```azurecli
      az account list -o table
      ```

      O comando **az account list** retorna a lista de assinaturas disponíveis para seu logon. 
      Se houver mais de uma, identifique o valor de ID da assinatura da assinatura que deseja usar.

   1. Defina a assinatura do Azure que deseja usar como a conta padrão:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      em que \<your-subscription-id\> é o valor da ID da assinatura que você deseja usar. Não inclua os colchetes.

   1. Confirme a nova configuração da assinatura solicitando os detalhes da assinatura atual. 

      ```azurecli
      az account show
      ```    

1. Na janela da CLI, instale a biblioteca de plotagem do Python, **matplotlib**, caso ainda não a tenha.

   ```azurecli
   pip install matplotlib
   ```

1. Na janela da CLI, envie o script **iris_sklearn.py** como um experimento.

   A execução do iris_sklearn.py é executada no contexto de computação local.

   + No Windows:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + No MacOS:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   Sua saída deverá ser semelhante a:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
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
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Examine a saída. Você tem a mesma saída e os mesmos resultados que tinha quando usou o Workbench para executar o script. 

1. Na janela da CLI, execute novamente o script do Python, **iris_sklearn.py**, usando um ambiente de execução do Docker (se você tiver o Docker instalado no seu computador).

   + Se seu contêiner estiver no Windows: 
     |Execução<br/>environment|Comando do Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Se seu contêiner estiver no MacOS: 
     |Execução<br/>environment|Comando do Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Volte para o Workbench e:
   1. Selecione o ícone de pasta no painel esquerdo para listar os arquivos de projeto.
   
   1. Abra o script do Python chamado **run.py**. Esse script é útil para executar um loop sobre várias taxas de regularização. 

   ![Retornar à Lista de Execuções](media/tutorial-classifying-iris/2-runpy.png)

1. Execute o experimento várias vezes com essas taxas. 

   Esse script inicia um trabalho `iris_sklearn.py` com uma taxa de regularização de `10.0` (um número absurdamente grande). O script, em seguida, corta a taxa pela metade na execução seguinte, e assim por diante, até que a taxa não fique menor do que `0.005`. 

   O script contém o código a seguir:

   ![Retornar à Lista de Execuções](media/tutorial-classifying-iris/2-runpy-code.png)

1. Execute o script **run.py** a partir da linha de comando conforme a seguir:

   ```cmd
   python run.py
   ```

   Esse comando envia o iris_sklearn.py várias vezes com taxas diferentes de regularização

   Quando `run.py` for concluída, você pode ver gráficos de métricas diferentes na sua exibição de lista de histórico de execução no Workbench.

## <a name="run-scripts-in-a-remote-docker-container"></a>Executar scripts em um contêiner do Docker remoto
Para executar o script em um contêiner do Docker em um computador Linux remoto, você precisa ter acesso SSH (nome de usuário e senha) para esse computador remoto. Além disso, o computador deve ter o mecanismo do Docker instalado e em execução. A maneira mais fácil de obter um computador Linux desse tipo é criando uma Máquina Virtual de Ciência de Dados (DSVM) baseada no Ubuntu no Azure. Saiba [como criar uma DSVM do Ubuntu para usar no Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>*Não* há suporte para DSVM com base em CentOS.

1. Depois que a VM é criada, você pode anexar a VM como um ambiente de execução gerando um par de arquivos `.runconfig` e `.compute`. Execute o comando a seguir para gerar a chave. 

 Vamos nomear o novo destino de computação como `myvm`.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >A área de endereço IP também pode ser um FQDN (nome de domínio totalmente qualificado) publicamente endereçável, como `vm-name.southcentralus.cloudapp.azure.com`. É uma boa prática adicionar um FQDN à sua DSVM e usá-lo no lugar de um endereço IP. Essa prática é uma boa ideia de como você pode desativar a VM em algum momento para economizar custos. Além disso, na próxima vez que você iniciar a VM, o endereço IP poderá ter sido alterado.

   >[!NOTE]
   >Além da autenticação de usuário e senha, você pode especificar uma chave privada e a senha correspondente (se houver) usando as opções `--private-key-file` e (opcionalmente) `--private-key-passphrase`. Se você quiser usar a chave privada que você usou ao criar DSVM, especifique a opção `--use-azureml-ssh-key`.

   Em seguida, prepare o destino de computação **myvm** executando este comando.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   O comando a seguir constrói a imagem do Docker na VM para prepará-la para executar os scripts.
   
   >[!NOTE]
   >Você também pode alterar o valor de `PrepareEnvironment` em `myvm.runconfig` do valor padrão `false` para `true`. Isso altera automaticamente o contêiner de Docker como parte da primeira execução.

2. Edite o arquivo `myvm.runconfig` gerado sob `aml_config` e altere a estrutura do valor padrão `PySpark` para `Python`:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Embora PySpark também deva funcionar, usar Python é mais eficiente se não precisar realmente de uma sessão do Spark para executar o script de Python.

3. Envie o mesmo comando enviado na janela da CLI, desta vez usando o destino _myvm_ para executar o iris_sklearn.py em um contêiner do Docker remoto:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   O comando é executado como se você estivesse em um ambiente `docker-python`, exceto pelo fato de que a execução ocorre na VM Linux remota. A janela CLI exibe as mesmas informações de saída.

4. Vamos tentar usar o Spark no contêiner. Abra o Explorador de Arquivos. Faça uma cópia do arquivo `myvm.runconfig` e nomeie-o `myvm-spark.runconfig`. Edite o novo arquivo para alterar a configuração `Framework` de `Python` para `PySpark`:
   ```yaml
   Framework: PySpark
   ```
   Não faça nenhuma alteração ao arquivo `myvm.compute`. A mesma imagem do Docker na mesma VM é usada para a execução do Spark. No novo `myvm-spark.runconfig`, o campo `Target` aponta para o mesmo arquivo `myvm.compute` por meio de seu nome `myvm`.

5. Digite o seguinte comando para executar o script **iris_spark.py** na instância do Spark em execução dentro do contêiner Docker remoto:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Executar scripts em clusters HDInsight
Você também pode executar esse script em um cluster HDInsight Spark. Saiba [como criar um cluster HDInsight Spark para usar no Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>O cluster HDInsight deve usar o Blob do Azure como o armazenamento primário. O uso do armazenamento do Azure Data Lake ainda não tem suporte.

1. Se você tiver acesso a um cluster Spark para o Azure HDInsight, gere um comando de configuração de execução HDInsight, conforme mostrado aqui. Forneça o nome do cluster HDInsight, seu nome de usuário HDInsight e a senha como parâmetros. 

   Use o comando a seguir para criar um destino de computação que aponte para um cluster HDInsight:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Para preparar o cluster HDInsight, execute este comando:

   ```
   az ml experiment prepare -c myhdi
   ```

   O nó principal do cluster FQDN normalmente é `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >O `username` é o nome de usuário do cluster SSH definido durante a instalação do HDInsight. Por padrão, o valor é `sshuser`. O valor não é `admin`, que é o outro usuário criado durante a configuração para habilitar o acesso ao site de administração do cluster. 

2. Execute o script **iris_spark.py** no cluster HDInsight com este comando:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Quando você executar em um cluster HDInsight remoto, você também poderá exibir os detalhes do Outro Negociador de Recursos (YARN) em `https://<your_cluster_name>.azurehdinsight.net/yarnui` usando a conta de usuário `admin`.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas
Nesta segunda parte da série de tutoriais de três partes, você aprendeu como:
> [!div class="checklist"]
> * Abrir scripts e examinar o código no Workbench
> * Executar scripts em um ambiente local
> * Consultar o histórico de execução
> * Executar scripts em um ambiente do Docker local

Agora, você pode testar a terceira parte desta série de tutoriais, na qual é possível implantar o modelo de regressão logística criado por você como um serviço Web em tempo real.

> [!div class="nextstepaction"]
> [Tutorial 3 – Implantar modelos](tutorial-classifying-iris-part-3.md)
