---
title: Usar notebooks Zeppelin com cluster Apache Spark no Azure HDInsight
description: Instruções passo a passo sobre como usar notebooks Zeppelin com clusters Apache Spark no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 70d1e08b1b81fd3aba159e18de3ee8829bc36548
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331003"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Use os cadernos Apache Zeppelin com o cluster do Apache Spark no HDInsight do Azure

Os clusters do HDInsight Spark incluem os [blocos de anotações do Apache Zeppelin](https://zeppelin.apache.org/) que podem ser usados para executar os trabalhos [do Apache Spark](https://spark.apache.org/). Neste artigo, você aprenderá a usar o notebook Zeppelin em um cluster HDInsight.

**Pré-requisitos:**

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-an-apache-zeppelin-notebook"></a>Inicie um notebook do Apache Zeppelin
1. Na folha do cluster Spark, clique em **Painel do Cluster** e em **Notebook Zeppelin**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.
   
   > [!NOTE]  
   > Você também pode acessar o Bloco de Notas Zeppelin de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

1. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Notebook** e em **Criar Nova Anotação**.
   
    ![Criar um novo notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Criar um novo notebook Zeppelin")
   
    Insira um nome para o notebook e, em seguida, clique em **Criar Anotação**.
1. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status conectado. Isso é indicado por um ponto verde no canto superior direito.
   
    ![Status do notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Status do notebook Zeppelin")
1. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\HdiSamples\SensorSampleData\hvac**.
   
    No parágrafo vazio criado por padrão no novo bloco de anotações, cole o snippet a seguir.
   
        %livy2.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
   
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
   
    Pressione **SHIFT + ENTER** ou clique no botão **Reproduzir** para o parágrafo executar o snippet. O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXCECUÇÃO para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:
   
    ![Criar uma tabela temporária a partir de dados brutos](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Criar uma tabela temporária a partir de dados brutos")
   
    Você também pode fornecer um título para cada parágrafo. No canto direito, clique no ícone **Configurações** e em **Mostrar título**.

> [!NOTE]  
> O intérprete% spark2 não é suportado nos portáteis Zeppelin em todas as versões do HDInsight, e o intérprete% sh não será suportado pelo HDInsight 4.0 em diante.

1. Agora você pode executar instruções do Spark SQL na tabela **hvac** . Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID do prédio e a diferença entre as temperaturas almejada e real para cada prédio em uma determinada data. Pressione **SHIFT+ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    A instrução **%sql** no início informa ao bloco de anotações para usar o interpretador Scala Livy.
   
    A captura de tela a seguir mostra o resultado.
   
    ![Executar uma instrução SQL do Spark usando o notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Executar uma instrução SQL do Spark usando o notebook")
   
     Clique nas opções de exibição (realçadas no retângulo) para alternar entre diferentes representações para o mesmo resultado. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa **buildingID** como a chave e a média de **temp_diff** como o valor.
1. Você também pode executar instruções Spark SQL usando variáveis na consulta. O seguinte snippet mostra como definir uma variável, **Temp**, na consulta com os possíveis valores com os quais você deseja consultar. Quando você executa a consulta pela primeira vez, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Cole esse snippet em um novo parágrafo e pressione **SHIFT+ENTER**. A captura de tela a seguir mostra o resultado.
   
    ![Executar uma instrução SQL do Spark usando o notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Executar uma instrução SQL do Spark usando o notebook")
   
    Em consultas subsequentes, você pode selecionar um novo valor na lista suspensa e executar a consulta novamente. Clique em **Configurações** para escolher o que constitui a chave e os valores na saída. A captura de tela acima usa o **buildingID** como chave, a média de **temp_diff** como valor e a **targettemp** como grupo.
1. Reinicie o interpretador Livy para sair do aplicativo. Para fazer isso, abra as configurações do interpretador clicando no nome do usuário conectado no canto superior direito e clique em **Interpretador**.
   
    ![Iniciar o intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do Hive")
1. Role até as configurações do interpretador Livy e clique em **Reiniciar**.
   
    ![Reiniciar o intérprete do Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reiniciar o intérprete do Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como usar pacotes externos com o notebook?
Você pode configurar o Zeppelin no cluster Apache Spark no HDInsight (Linux) para usar pacotes externos enviados pela comunidade que não estão incluídos prontos no cluster. Você pode pesquisar o [Repositório do Maven](https://search.maven.org/) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes enviados pela comunidade está disponível em [Pacotes do Spark](https://spark-packages.org/).

Neste artigo, você aprenderá a usar o pacote [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de notas Jupyter.

1. Abra as configurações do interpretador. No canto superior direito, clique no nome do usuário conectado e clique em **Interpretador**.
   
    ![Iniciar o intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do Hive")
1. Vá para configurações do interpretador Livy e clique em **Editar**.
   
    ![Alterar configurações do intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Alterar configurações do intérprete")
1. Adicionar uma nova chave chamada **livy.spark.jars.packages** e defina seu valor no formato `group:id:version`. Dessa forma, se você quiser usar o pacote [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), deverá definir o valor da chave como `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Alterar configurações do intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Alterar configurações do intérprete")
   
    Clique em **Salvar** e reinicie o interpretador Livy.
1. **Dica**: se quiser saber como chegar ao valor da chave inserida acima, confira como.
   
     a. Localize o pacote no Repositório Maven. Para este tutorial, usamos [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. No repositório, colete os valores para **GroupId**, **ArtifactId** e **Version**.
   
    ![Usar pacotes externos com blocos de anotações do Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Usar pacotes externos com blocos de anotações do Jupyter")
   
    c. Concatene os três valores, separados por dois pontos (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde os notebooks Zeppelin são salvos?
Os notebooks Zeppelin são salvos nos nós de cabeçalho do cluster. Portanto, se você excluir o cluster, os notebooks também serão excluídos. Se você quiser preservar os notebooks para uso posterior em outros clusters, deverá exportá-los depois de concluir os trabalhos em execução. Para exportar um notebook, clique no ícone **Exportar** como mostrado na imagem abaixo.

![Baixar o notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Baixar o notebook")

Isso salva o notebook como um arquivo JSON em seu local de download.

## <a name="livy-session-management"></a>Gerenciamento de sessões do Livy
Quando você executa o primeiro parágrafo de código no notebook Zeppelin, uma nova sessão do Livy é criada em seu cluster HDInsight Spark. Essa sessão será compartilhada entre todos os notebooks Zeppelin que você criar posteriormente. Se por algum motivo a sessão do Livy for interrompida (reinicialização do cluster etc.), você não poderá executar trabalhos no notebook Zeppelin.

Nesse caso, você deverá executar as etapas a seguir antes de iniciar a execução de trabalhos de um notebook do Zeppelin. 

1. Reinicie o interpretador Livy no notebook Zeppelin. Para fazer isso, abra as configurações do interpretador clicando no nome do usuário conectado no canto superior direito e clique em **Interpretador**.
   
    ![Iniciar o intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do Hive")
1. Role até as configurações do interpretador Livy e clique em **Reiniciar**.
   
    ![Reiniciar o intérprete do Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reiniciar o intérprete do Zeppelin")
1. Execute uma célula de código de um notebook Zeppelin existente. Isso cria uma nova sessão do Livy no cluster HDInsight.

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA criar e enviar os aplicativos Scala do Apache Spark](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







