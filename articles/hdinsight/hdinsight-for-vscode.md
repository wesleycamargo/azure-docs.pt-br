---
title: Ferramentas do Azure HDInsight – Use o Visual Studio Code para o Hive, o LLAP ou PySpark | Microsoft Docs
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: f8bd1f1181cbd592782ce1126d5d61b5f257ca08
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234741"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Usar a Ferramenta do Azure HDInsight para Visual Studio Code

Aprenda a usar o Azure HDInsight Tools para código do Visual Studio (código VS) para criar e enviar tarefas em lote do Apache Hive, consultas interativas do Apache Hive e scripts do PySpark. As Ferramentas do Azure HDInsight podem ser instaladas em plataformas compatíveis com VS Code. Isso inclui Windows, Linux e macOS. Você pode encontrar os pré-requisitos para diferentes plataformas.


## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster HDInsight. Para criar um cluster, consulte [Introdução ao HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e MacOS.

## <a name="install-the-hdinsight-tools"></a>Instalar as Ferramentas do HDInsight
   
Depois de instalar os pré-requisitos, você pode instalar as Ferramentas do Azure HDInsight para VS Code. 

### <a name="to-install-azure-hdinsight-tools"></a>Para instalar as Ferramentas do Azure HDInsight

1. Abra o Visual Studio Code.

2. No painel esquerdo, selecione **Extensões**. Digite **HDInsight** na caixa de pesquisa.

3. Ao lado de **Ferramentas do Azure HDInsight**, selecione **Instalar**. Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.

4. Selecione **Recarregar** para ativar a extensão **Ferramentas do Azure HDInsight**.

5. Clique em **Recarregar Janela** para confirmar. Você pode ver as **Ferramentas do Azure HDInsight** no painel **Extensões**.

   ![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Abrir o workspace do HDInsight

Crie um workspace no VSCode para poder se conectar ao Azure.

### <a name="to-open-a-workspace"></a>Para abrir um workspace

1. No menu **Arquivo**, selecione **Abrir Pasta**. Em seguida, designe uma pasta existente como a pasta de trabalho ou crie uma novo. A pasta aparece no painel esquerdo.

2. No painel esquerdo, selecione o ícone **Novo arquivo** ao lado da pasta de trabalho.

   ![Novo arquivo](./media/hdinsight-for-vscode/new-file.png)

3. Nomeie o novo arquivo com a extensão de arquivo .hql (consultas de Hive) ou .py (script Spark). 

## <a name="connect-to-hdinsight-cluster"></a>Conectar ao Cluster HDInsight

Antes de enviar scripts para clusters HDInsight a partir do VS Code, você precisa se conectar à sua conta do Azure ou vincular um cluster (usando o nome de usuário, senha ou conta de domínio associado).

### <a name="to-connect-to-azure"></a>Para se conectar ao Azure

1. Crie uma nova pasta de trabalho e um novo arquivo de script, se ainda não os tiver.

2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Logon** no menu de contexto. Você também pode inserir **Ctrl+Shift+P** e, em seguida, digitar **HDInsight: Logon**.

    ![Logon das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Para entrar, siga as instruções de conexão no painel **SAÍDA**.
    + Para o ambiente global, a entrada no HDInsight dispara o processo de entrada do Azure.

        ![Instruções de entrada no Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Para outros ambientes, siga as instruções de entrada.

        ![Instruções de entrada para outro ambiente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

    Depois que você estiver conectado, o nome da conta do Azure será mostrado na barra de status no canto inferior esquerdo da janela do VS Code. 

    > [!NOTE]
    > Devido a um problema conhecido de autenticação do Azure, você precisa abrir um navegador em modo privado ou incógnito. Se a sua conta do Azure tem dois fatores habilitados, é recomendável usar a autenticação por telefone, em vez da autenticação por PIN.
  

4. Clique com o botão direito do mouse no editor de scripts para abrir o menu de contexto. Do menu de contexto, você pode executar as seguintes tarefas:

    - Faça logoff
    - Listar clusters
    - Definir clusters padrão
    - Enviar consultas interativas do Hive
    - Enviar scripts em lotes do Hive
    - Enviar consultas interativas do PySpark
    - Enviar os scripts em lote do PySpark
    - Definir a configuração

<h3 id="linkcluster">Para vincular um cluster</h3>

Você pode vincular um cluster normal usando um nome de usuário gerenciado do Apache Ambari ou vincular um cluster Hadoop seguro do Enterprise Security Pack usando um nome de usuário de domínio (como: user1@contoso.com).
1. Abra a paleta de comandos selecionando **CTRL+SHIFT+P** e, em seguida, insira **HDInsight: Vincular um Cluster**.

   ![comando para vincular cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Insira a URL do Cluster HDInsight -> Nome de usuário de entrada -> Senha de entrada -> selecione o tipo de cluster -> informações de êxito serão exibidas se a verificação for aprovada.
   
   ![caixa de diálogo para vincular cluster](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > O nome de usuário e a senha vinculados serão usados se o cluster foi registrado na assinatura do Azure e vinculou um cluster. 
   
3. É possível ver um cluster vinculado usando o comando **List Cluster**. Agora, você pode enviar um script para esse cluster vinculado.

   ![cluster vinculado](./media/hdinsight-for-vscode/linked-cluster.png)

4. Você também pode desvincular um cluster inserindo **HDInsight: Desvincular um Cluster** da paleta de comandos.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Para vincular um ponto de extremidade genérico do Apache Livy

1. Abra a paleta de comandos selecionando **CTRL+SHIFT+P** e, em seguida, insira **HDInsight: Vincular um Cluster**.
2. Selecione **Ponto de Extremidade do Livy Genérico**.
3. Digite o terminal genérico Livy, por exemplo: http://10.172.41.42:18080.
4. Selecione **Básico** quando precisar de autorização para o terminal genérico Livy, caso contrário, selecione **Nenhum**.
5. Insira o nome de usuário quando selecionar **Básico** na etapa 4.
6. Insira a senha quando selecionar **Básico** na etapa 4.
7. O ponto de extremidade do Livy genérico foi vinculado com êxito.

   ![cluster do Livy genérico vinculado](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>Listar clusters HDInsight

Para testar a conexão, você pode listar seus clusters HDInsight:

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Para listar os clusters HDInsight na assinatura do Azure
1. Abra um workspace e conecte-se ao Azure. Para obter mais informações, consulte [Abrir workspace do HDInsight](#open-hdinsight-workspace) e [Conectar-se ao Azure](#connect-to-hdinsight-cluster).

2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Listar Clusters** no menu de contexto. 

3. Os clusters do HDInsight aparecem no painel **Saída**.

    ![Definir uma configuração do cluster padrão](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Definir um cluster padrão
1. Abra um workspace e conecte-se ao Azure. Veja [Abrir workspace do HDInsight](#open-hdinsight-workspace) e [Conectar-se ao Azure](#connect-to-hdinsight-cluster).

2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Definir Cluster Padrão**. 

3. Selecione um cluster como o padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o arquivo de configuração **.VSCode\settings.json**. 

   ![Definir configuração do cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure
1. Abra a paleta de comandos selecionando **CTRL+SHIFT+P**.

2. Entre em **HDInsight: Definir o Ambiente do Azure**.

3. Selecione um ambiente, como "Azure" ou "AzureChina" como sua entrada de login padrão.

4. Enquanto isso, a ferramenta já salvou sua entrada de logon padrão em **.VSCode\settings.json**. Você também a atualiza diretamente nesse arquivo de configuração. 

   ![Definir a configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Enviar consultas interativas do Hive, scripts em lotes do Hive

Com as Ferramentas do HDInsight para VS Code, você pode enviar consultas interativas do Hive e scripts em lotes do Hive a clusters do HDInsight.

1. Crie uma nova pasta de trabalho e um novo arquivo de script do Hive, se você não os tiver.

2. Conecte-se à sua conta do Azure ou vincule clusters.

3. Copie e cole o código a seguir no arquivo do Hive e depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Clique com o botão direito do mouse no editor de scripts e selecione **HDInsight: Hive Interativo** para enviar a consulta ou use o atalho **Ctrl + Alt + I**. Selecione **HDInsight: Lote do Hive** para enviar o script ou use o atalho **Ctrl + Alt + H**. 

5. Selecione o cluster se você ainda não especificou um cluster padrão. As ferramentas também permitem que você envie um bloco de código, em vez do arquivo de script inteiro, usando o menu de contexto. Após alguns instantes, os resultados da consulta aparecem em uma nova guia.

   ![Resultado do Hive interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel **RESULTADOS**: você pode salvar o resultado inteiro como um arquivo CSV, JSON ou Excel para o caminho local ou apenas selecionar várias linhas.

    - Painel **MENSAGENS**: ao selecionar o número de **Linha**, ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas do PySpark

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>Para enviar consultas interativas do PySpark para clusters do HDInsight Spark.

1. Crie uma nova pasta de trabalho e um novo arquivo de script com a extensão .py, se você ainda não os tiver.

2. Conecte-se à sua conta do Azure se ainda não tiver feito isso.

3. Copie e cole o seguinte código no arquivo de script:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Realçar esse script. Em seguida, clique com o botão direito do mouse no editor de scripts e selecione **HDInsight: PySpark Interativo** ou use o atalho **Ctrl + Alt + I**.

5. Se você ainda não tiver instalado a extensão do **Python** no VS Code, selecione o botão **Instalar** como mostrado na ilustração a seguir:

    ![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Instale o ambiente de Python no seu sistema, se ainda não tiver feito isso. 
   - Para Windows, baixe e instale o [Python](https://www.python.org/downloads/). Em seguida, verifique se `Python` e `pip` estão no PATH do sistema.

   - Para instruções para macOS e Linux, consulte [Configurar o ambiente interativo do PySpark para o Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Selecione um cluster ao qual enviar a consulta do PySpark. Logo depois, o resultado da consulta será mostrado na nova guia à direita:

   ![Enviar o resultado do trabalho do Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. A ferramenta também é compatível com a consulta **Cláusula SQL**.

   ![Enviar resultado do trabalho do Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) O status de envio aparece à esquerda da barra de status inferior ao executar consultas. Não envie outras consultas quando o status for **PySpark Kernel (ocupado)**. 

>[!NOTE]
>Os clusters podem manter as informações da sessão. A variável definida, a função e os valores correspondentes são mantidos na sessão para que possam ser consultados em várias chamadas de serviço para o mesmo cluster. 

### <a name="to-disable-environment-check"></a>Para desabilitar a verificação do ambiente

Por padrão, as ferramentas do HDInsight verificam o ambiente e instalam pacotes dependentes ao enviar consultas interativas do PySpark. Para desabilitar a verificação do ambiente, defina **hdinsight.disablePysparkEnvironmentValidation** como **sim** em **CONFIGURAÇÕES DE USUÁRIO**.

   ![Definir a verificação do ambiente de configurações](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

Como alternativa, clique no botão **Desabilitar Validação** quando a caixa de diálogo for exibida.

   ![Definir a verificação do ambiente da caixa de diálogo](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 não é compatível com Spark2.2/2.3

O PySpark3 não é mais compatível com o cluster do Spark 2.2 e Spark 2.3. Somente "PySpark" é compatível com o Python. Trata-se de um problema conhecido que envia para o Spark 2.2/2.3 a falha com o Python3.

   ![Enviar para o python3 o erro get](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Siga as etapas para usar o Python2.x: 

1. Instale o Python 2.7 no computador local e adicione-o ao caminho do sistema.

2. Reinicie o VSCode.

3. Mude para o Python 2 clicando no **Python XXX** na barra de status e escolha o Python de destino.

   ![Selecione a versão do Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

## <a name="submit-pyspark-batch-job"></a>Enviar o trabalho em lotes de PySpark

1. Crie uma nova pasta de trabalho e um novo arquivo de script com a extensão .py, se você ainda não os tiver.

2. Conecte-se à sua conta do Azure caso ainda não tenha feito isso.

3. Copie e cole o seguinte código no arquivo de script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Clique com o botão direito do mouse no editor de scripts e selecione **HDInsight: Lote do PySpark** ou use o atalho **Ctrl + Alt + H**. 

5. Selecione um cluster ao qual enviar seu trabalho PySpark. 

   ![Enviar o resultado do trabalho do Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois que você envia um trabalho Python, os logs de envio aparecem na janela **SAÍDA** no VS Code. A **URL de interface do usuário do Spark** e a **URL de interface do usuário do Yarn** também são mostradas. Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.

## <a name="livy-configuration"></a>Configuração de Livy

A configuração de Livy é compatível. É possível defini-la no **.VSCode\settings.json** na pasta do workspace. Atualmente, a configuração de Livy é compatível apenas com script do Python. Para obter mais detalhes, consulte o [LEIAME do Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Como disparar a configuração de Livy**
   
Você pode encontrar no menu **Arquivo** menu, selecionas **Preferências** e escolher **Configurações** no menu de contexto. Clique na guia **CONFIGURAÇÕES DE WORKSPACE** para poder iniciar a configuração do Livy.

Você também pode enviar um arquivo. Observe que a pasta. vscode é adicionada automaticamente à pasta de trabalho. Você pode encontrar a configuração de Livy clicando em **.vscode\settings.json**.

+ As configurações do projeto:

    ![Configuração de Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Para as configurações **driverMomory** e **executorMomry**, defina o valor com unidade, por exemplo, 1 g ou 1.024 m. 

+ As configurações de Livy compatíveis:   

    **POST /batches**   
    Corpo da solicitação

    | Nome | Descrição | Tipo | 
    | :- | :- | :- | 
    | file | Arquivo que contém o aplicativo a ser executado | caminho (obrigatório) | 
    | proxyUser | Usuário a ser representado ao executar o trabalho | string | 
    | className | Classe principal Java/Spark do aplicativo | string |
    | args | Argumentos de linha de comando do aplicativo | lista de cadeias de caracteres | 
    | jars | jars a serem usados nesta sessão | Lista de cadeias de caracteres | 
    | pyFiles | Arquivos Python a serem usados nesta sessão | Lista de cadeias de caracteres |
    | de entrada | arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | driverMemory | Quantidade de memória a ser usada para o processo de driver | string |
    | driverCores | Quantidade de núcleos a ser usado para o processo de driver | int |
    | executorMemory | Quantidade de memória a ser usada por processo de executor | string |
    | executorCores | Número de núcleos a serem usados para cada executor | int |
    | numExecutors | Número de executores a serem iniciados para esta sessão | int |
    | archives | Arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | fila | O nome da fila YARN ao qual foi enviado | string |
    | Nome | O nome desta sessão | string |
    | conf | Propriedades de configuração do Spark | Mapa de key=val |

    Corpo da resposta   
    O objeto de lote criado.

    | Nome | Descrição | Tipo | 
    | :- | :- | :- | 
    | ID | A id da sessão | int | 
    | appId | A ID de aplicativo desta sessão |  Cadeia de caracteres |
    | appInfo | As informações detalhadas do aplicativo | Mapa de key=val |
    | log | As linhas do log | lista de cadeias de caracteres |
    | state |   O estado do lote | string |

>[!NOTE]
>A configuração de Livy atribuída será exibida no painel de saída ao enviar o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar ao Azure HDInsight pelo Explorer

O Azure HDInsight foi adicionado ao painel esquerdo. Você pode procurar e gerenciar o cluster diretamente.

1. Expanda o **AZURE HDINSIGHT**. Se você não tiver entrado, ele mostrará o link **Entrar no Azure...**.

    ![Imagem do link de entrada](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Clique em **Entrar no Azure**. Ele mostra o link de entrada e o código na parte inferior direita.

    ![Instruções de entrada para outro ambiente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Clique no botão **Copiar e Abrir** para abrir o navegador e colar o código. Clique no botão **Continuar** e, em seguida, você verá a dica sobre como entrar com êxito.

4. Depois que você conectar, as assinaturas e os clusters disponíveis (Spark, Hadoop e HBase são compatíveis) serão listados no **AZURE HDINSIGHT**. 

   ![Assinatura do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Expanda o cluster para exibir o esquema de banco de dados de metadados e tabela do Hive.

   ![Cluster do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>Recursos adicionais

O HDInsight para VSCode é compatível com os seguintes recursos:

- **Preenchimento automático do IntelliSense**. Sugestões aparecem em pop-up para palavras-chave, métodos, variáveis e assim por diante. Os diferentes ícones representam diferentes tipos dos objetos.

    ![Tipos de objeto do IntelliSense das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha os erros de edição do script do Hive.     
- **Destaques da sintaxe**. O serviço de linguagem usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções e assim por diante. 

    ![Destaques da sintaxe das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Próximas etapas

### <a name="demo"></a>Demonstração
* HDInsight para VS Code: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio do SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Configurar o ambiente interativo do PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Usar o Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Criando e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](spark/apache-spark-job-debugging.md)



