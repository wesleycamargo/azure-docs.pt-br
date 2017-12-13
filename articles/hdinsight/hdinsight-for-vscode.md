---
title: "Ferramentas do Azure HDInsight – use o Visual Studio Code para o Hive, o LLAP ou pySpark | Microsoft Docs"
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: d6ca3bcb91261a863444bc331c78adf44844be56
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Usar a Ferramenta do Azure HDInsight para Visual Studio Code

Saiba como usar as Ferramentas do Azure HDInsight para VS Code (Visual Studio Code) para criar e enviar trabalhos em lotes do Hive, consultas interativas do Hive e scripts pySpark. As Ferramentas do Azure HDInsight podem ser instaladas em plataformas compatíveis com VS Code. Isso inclui Windows, Linux e macOS. Você pode encontrar os pré-requisitos para diferentes plataformas.


## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster HDInsight.  Para criar um cluster, consulte [Introdução ao HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e MacOS.

## <a name="install-the-hdinsight-tools"></a>Instalar as Ferramentas do HDInsight
   
Depois de instalar os pré-requisitos, você pode instalar as Ferramentas do Azure HDInsight para VS Code. 

**Para instalar as Ferramentas do Azure HDInsight**

1. Abra o Visual Studio Code.

2. No painel esquerdo, selecione **Extensões**. Digite **HDInsight** na caixa de pesquisa.

3. Ao lado de **Ferramentas do Azure HDInsight**, selecione **Instalar**. Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.

4. Selecione **Recarregar** para ativar a extensão **Ferramentas do Azure HDInsight**.

5. Clique em **Recarregar Janela** para confirmar. Você pode ver as **Ferramentas do Azure HDInsight** no painel **Extensões**.

   ![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Abrir o espaço de trabalho do HDInsight

Crie um espaço de trabalho no VSCode para poder se conectar ao Azure.

**Para abrir um espaço de trabalho**

1. No menu **Arquivo**, selecione **Abrir Pasta**. Em seguida, designe uma pasta existente como a pasta de trabalho ou crie uma novo. A pasta aparece no painel esquerdo.

2. No painel esquerdo, selecione o ícone **Novo arquivo** ao lado da pasta de trabalho.

   ![Novo arquivo](./media/hdinsight-for-vscode/new-file.png)

3. Nomeie o novo arquivo com a extensão de arquivo .hql (consultas de Hive) ou .py (script Spark). Observe que um arquivo de configuração **XXXX_hdi_settings.json** é adicionado automaticamente à pasta de trabalho.

4. Abra **XXXX_hdi_settings.json** do **EXPLORER** ou clique com o botão direito do mouse no editor de scripts para selecionar **Definir Configuração**. Você pode configurar a entrada de logon, o cluster padrão e os parâmetros de envio do trabalho, conforme visto na amostra no arquivo. Você também pode deixar os parâmetros restantes vazios.

## <a name="connect-to-azure"></a>Conecte-se ao Azure

Antes que você possa enviar scripts a clusters do HDInsight do VS Code, você precisará se conectar à sua conta do Azure.

**Para se conectar ao Azure**

1. Crie uma nova pasta de trabalho e um novo arquivo de script, se ainda não os tiver.

2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Logon** no menu de contexto. Você também pode inserir **Ctrl+Shift+P** e, em seguida, digitar **HDInsight: Logon**.

    ![Logon das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Para entrar, siga as instruções de conexão no painel **SAÍDA**.

    **Azure:** ![informações de logon das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

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
    - Definir configurações

## <a name="list-hdinsight-clusters"></a>Listar clusters HDInsight

Para testar a conexão, você pode listar seus clusters HDInsight:

**Para listar os clusters HDInsight na assinatura do Azure**
1. Abra um espaço de trabalho e conecte-se ao Azure. Para obter mais informações, consulte [Abrir espaço de trabalho do HDInsight](#open-hdinsight-workspace) e [Conectar-se ao Azure](#connect-to-azure).

2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Listar Clusters** no menu de contexto. 

3. Os clusters Hive e Spark aparecem no painel **Saída**.

    ![Definir uma configuração do cluster padrão](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Definir um cluster padrão
1. Abra um espaço de trabalho e conecte-se ao Azure. Veja [Abrir espaço de trabalho do HDInsight](#open-hdinsight-workspace) e [Conectar-se ao Azure](#connect-to-azure).

2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Definir Cluster Padrão**. 

3. Selecione um cluster como o padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o arquivo de configuração, **XXXX_hdi_settings.json**. 

   ![Definir configuração do cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure 
1. Abra a paleta de comandos selecionando **CTRL+SHIFT+P**.

2. Entre em **HDInsight: Definir o Ambiente do Azure**.

3. Selecione um modo entre Azure e AzureChina como sua entrada de logon padrão.

4. Enquanto isso, a ferramenta já salvou sua entrada de logon padrão em **XXXX_hdi_settings.json**. Você também a atualiza diretamente nesse arquivo de configuração. 

   ![Definir a configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Enviar consultas interativas do Hive

Com as Ferramentas do HDInsight para VS Code, você pode enviar consultas interativas do Hive a clusters de consulta interativa do HDInsight.

1. Crie uma nova pasta de trabalho e um novo arquivo de script do Hive, se você não os tiver.

2. Conecte-se à sua conta do Azure e, em seguida, configure o cluster padrão, se você ainda não tiver feito isso.

3. Copie e cole o código a seguir no arquivo do Hive e depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Clique com o botão direito do mouse no editor de scripts e, em seguida, clique em **HDInsight: Hive Interativo** para enviar a consulta. As ferramentas também permitem que você envie um bloco de código, em vez do arquivo de script inteiro, usando o menu de contexto. Logo depois, os resultados da consulta aparecem em uma nova guia.

   ![Resultado do Hive interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel **RESULTADOS**: você pode salvar o resultado inteiro como um arquivo CSV, JSON ou Excel para o caminho local ou apenas selecionar várias linhas.

    - Painel **MENSAGENS**: ao selecionar o número de **Linha**, ele salta para a primeira linha do script em execução.

Executar a consulta interativa leva muito menos tempo que [executar um trabalho em lotes do Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Enviar scripts em lotes do Hive

1. Crie uma nova pasta de trabalho e um novo arquivo de script do Hive, se você não os tiver.

2. Conecte-se à sua conta do Azure e, em seguida, configure o cluster padrão, se você ainda não tiver feito isso.

3. Copie e cole o código a seguir no arquivo do Hive e depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: lote do Hive** para enviar um trabalho do Hive. 

4. Selecione o cluster ao qual você deseja enviar.  

    Depois de enviar um trabalho do Hive, as informações de sucesso de envio e jobid aparecem no painel **SAÍDA**. O trabalho do Hive também abre **NAVEGADOR DA WEB**, que mostra o status e os logs de trabalho em tempo real.

   ![enviar resultado de trabalho do Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Enviar consultas do Hive interativas](#submit-interactive-hive-queries) leva muito menos tempo do que enviar um trabalho em lotes.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas do PySpark
As Ferramentas do HDInsight para VS Code também permitem enviar consultas interativas do PySpark para clusters do Spark.
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
4. Realce estes scripts. Em seguida, clique com o botão direito do mouse no editor de scripts e selecione **HDInsight: PySpark Interativo**.

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
 

## <a name="submit-pyspark-batch-job"></a>Enviar o trabalho em lotes de PySpark

1. Crie uma nova pasta de trabalho e um novo arquivo de script com a extensão .py, se você ainda não os tiver.

2. Conecte-se à sua conta do Azure, caso ainda não tenha feito isso.

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
4. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Lote do PySpark**. 

5. Selecione um cluster ao qual enviar seu trabalho PySpark. 

   ![Enviar o resultado do trabalho do Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois de enviar um trabalho Python, os logs de envio aparecem na janela **SAÍDA** no VS Code. A **URL de interface do usuário do Spark** e a **URL de interface do usuário do Yarn** também são mostradas. Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.


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
* [Configurar o ambiente interativo do PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Usar o Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](spark/apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Criando e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](spark/apache-spark-job-debugging.md)



