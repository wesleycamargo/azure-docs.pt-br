---
title: Usar as ferramentas de BI com o Apache Spark no Azure HDInsight | Microsoft Docs
description: "Instruções passo a passo sobre como usar blocos de anotações com o Apache Spark para criar esquemas em dados brutos, salvá-los como tabelas Hive e, em seguida, usar ferramentas de BI na tabela Hive para análise de dados"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a3bdeb6fea306babc9358134c37044843b9bdd1c
ms.openlocfilehash: e31c947ee275d34b1f59909fb009ce5a73b42785


---
# <a name="use-bi-tools-with-apache-spark-cluster-on-azure-hdinsight"></a>Usar as ferramentas de BI com o cluster do Apache Spark no Azure HDInsight

Saiba como usar o Apache Spark no Azure HDInsight para fazer o seguinte:

* Pegar os dados brutos de exemplo e salvá-los como uma tabela Hive
* Usar ferramentas de BI como Power BI e Tableau para analisar e visualizar os dados.

Este tutorial também está disponível como um notebook Jupyter em um cluster do Spark (Linux) que você pode criar no HDInsight. A experiência de bloco de anotações permite executar os trechos de código Python no próprio bloco de anotações. Para executar o tutorial em um bloco de anotações, crie um cluster Spark, inicie um bloco de anotações do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e execute o bloco de anotações **Usar ferramentas de BI com Apache Spark no HDInsight.ipynb** na pasta **Python**.

**Pré-requisitos:**

Você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Um computador com o driver ODBC do Microsoft Spark instalado (necessário para que o Spark no HDInsight funcione com o Tableau). Você pode instalar o driver clicando [aqui](http://go.microsoft.com/fwlink/?LinkId=616229).
* Ferramentas de BI como [Power BI](http://www.powerbi.com/) ou [Tableau Desktop](http://www.tableau.com/products/desktop). Você pode obter uma assinatura gratuita de visualização do Power BI em [http://www.powerbi.com/](http://www.powerbi.com/).

## <a name="a-namehivetableasave-raw-data-as-a-hive-table"></a><a name="hivetable"></a>Salvar dados brutos como uma tabela Hive
Nesta seção, usamos o bloco de anotações do [Jupyter](https://jupyter.org) associado a um cluster Apache Spark no HDInsight para executar trabalhos que processam os dados brutos de exemplo e os salvam como uma tabela Hive. Os dados de exemplo são um arquivo .csv (hvac.csv) disponível em todos os clusters por padrão.

Depois que os dados são salvos como uma tabela Hive, na próxima seção, vamos nos conectar à tabela Hive usando ferramentas de BI como Power BI e Tableau.

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   
2. Na folha do cluster Spark, clique em **Painel do Cluster** e em **Notebook Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   > [!NOTE]
   > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um novo bloco de anotações. Clique em **Novo** e em **PySpark**.

    ![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de anotações do Jupyter")
4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

    ![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de anotações")
5. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos obrigatórios para este cenário. Para fazer isso, coloque o cursor na célula e pressione **SHIFT + ENTER**.

        from pyspark.sql import *
6. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Em uma célula vazia, cole o trecho a seguir e pressione **SHIFT+ENTER**. Esse trecho registra os dados em uma tabela Hive chamada **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

1. Verifique se a tabela foi criada com êxito. Você pode usar a mágica do `%%sql` para executar as consultas do Hive diretamente. Para obter mais informações sobre a mágica de `%%sql` , bem como outras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#choose-between-the-kernels).

        %%sql
        SHOW TABLES

    Você verá algo semelhante ao mostrado a seguir:

        +-----------+---------------+
        |isTemporary|tableName        |
        +-----------+---------------+
        |       true|hvactemptable  |
        |      false|hivesampletable|
        |      false|hvac            |
        +-----------+---------------+

    Somente as tabelas que têm falso sob a coluna **isTemporary** são tabelas hive que serão armazenadas no metastore e podem ser acessadas por meio das ferramentas de BI. Neste tutorial, vamos nos conectar à tabela **hvac** recém-criada.

1. Verifique se a tabela contém os dados pretendidos. Em uma célula vazia no bloco de anotações, copie o seguinte trecho e pressione **SHIFT+ENTER**.

        %%sql
        SELECT * FROM hvac LIMIT 10
2. Agora você pode encerrar o bloco de anotações para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.

## <a name="a-namepowerbiause-power-bi-to-analyze-data-in-the-hive-table"></a><a name="powerbi"></a>Usar o Power BI para analisar os dados da tabela Hive
Depois de salvar os dados como uma tabela Hive, você pode usar o Power BI para conectar aos dados e visualizá-los para criar relatórios, painéis, etc.

1. Entre no [Power BI](http://www.powerbi.com/).
2. Na tela de boas-vindas, clique em **Bancos de dados e mais**.

    ![Colocar dados no Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Colocar dados no Power BI")
3. Na próxima tela, clique em **Spark no Azure HDInsight** e em **Conectar**. Quando solicitado, insira a URL do cluster (`mysparkcluster.azurehdinsight.net`) e as credenciais para se conectar a ele

    Depois que a conexão é estabelecida, o Power BI inicia a importação de dados do cluster Spark no HDInsight.
4. O Power BI importa os dados e adiciona um novo conjunto de dados Spark sob o título **Conjuntos de dados** . Clique no conjunto de dados para abrir uma nova planilha e visualizar os dados. Também é possível salvar a planilha como um relatório. Para salvar uma planilha, no menu **Arquivo**, clique em **Salvar**.

    ![Bloco Spark no painel do Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Bloco Spark no painel do Power BI")
5. Observe que a lista **Campos**, à direita, lista a tabela **hvac** que você criou anteriormente. Expanda a tabela para ver seus campos, conforme você definiu anteriormente no bloco de anotações.

      ![Listar tabelas Hive](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Listar tabelas Hive")
6. Crie uma visualização para mostrar a variação entre a temperatura almejada e a temperatura real para cada edifício. Escolha **Gráfico de Áreas** (mostrado na caixa vermelha) para visualizar os dados. Para definir o eixo, arraste e solte o campo **BuildingID** em **Eixo** e os campos **ActualTemp**/**TargetTemp** em **Valor**.

    ![Criar visualizações](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "Criar visualizações")
7. Por padrão, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Para os campos, na lista suspensa, selecione **Média** para obter uma média das temperaturas almejada e real para ambos os prédios.

    ![Criar visualizações](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
8. A visualização de dados deve ser semelhante ao seguinte. Mova o cursor sobre a visualização para obter dicas de ferramenta com dados relevantes.

    ![Criar visualizações](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)
9. Clique em **Salvar** no menu superior e forneça um nome de relatório. Você também pode fixar o visual. Quando você fixar uma visualização, ela será armazenada no painel para poder controlar o valor mais recente rapidamente.

   Você pode adicionar quantas visualizações desejar para o mesmo conjunto de dados e fixá-las no painel para um instantâneo dos dados. Além disso, os clusters do Spark no HDInsight são conectados ao Power BI com conexão direta. Isso significa que o Power BI sempre tem as informações mais atualizadas de seu cluster para que você não precise agendar atualizações para o conjunto de dados.

## <a name="a-nametableauause-tableau-desktop-to-analyze-data-in-the-hive-table"></a><a name="tableau"></a>Usar o Tableau Desktop para analisar os dados da tabela Hive

> [!NOTE]
> Esta seção é aplicável somente para clusters do Spark 1.5.2 criados no Azure HDInsight.
>
>

1. Inicie o Tableau Desktop. No painel esquerdo, na lista de servidores aos quais se conectar, clique em **Spark SQL**. Se Spark SQL não estiver listado por padrão no painel esquerdo, você poderá encontrá-lo ao clicar em **Mais Servidores**.
2. Na caixa de diálogo de conexão do Spark SQL, forneça os valores conforme mostrado abaixo e clique em **OK**.

    ![Conectar a um cluster Spark](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Conectar a um cluster Spark")

    O menu suspenso de autenticação listará o **Serviço Microsoft Azure HDInsight** como uma opção somente se você tiver instalado o [Driver ODBC do Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) no computador.
3. Na próxima tela, no menu suspenso **Esquema**, clique no ícone **Localizar** e clique em **padrão**.

    ![Localizar esquema](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "Localizar esquema")
4. Para o campo **Tabela**, clique no ícone **Localizar** novamente para listar todas as tabelas Hive disponíveis no cluster. Você deve ver a tabela **hvac** criada anteriormente usando o bloco de anotações.

    ![Localizar tabelas](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "Localizar tabelas")
5. Arraste e solte a tabela para a caixa superior à direita. O Tableau importa os dados e exibe o esquema conforme destacado pela caixa vermelha.

    ![Adicionar tabelas ao Tableau](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Adicionar tabelas ao Tableau")
6. Clique na guia **Sheet1** na parte inferior esquerda. Faça uma visualização que mostre as temperaturas almejada e real para todos os edifícios para cada data. Arraste **Data** e **ID do Prédio** para **Colunas** e **Temp. real**/**Temp. almejada** para **Linhas**. Em **Marcas**, selecione **Área** para usar uma visualização do mapa de área.

     ![Adicionar campos para visualização](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "Adicionar campos para visualização")
7. Por padrão, os campos de temperatura são mostrados em agregado. Se você desejar mostrar a temperatura média em vez disso, pode fazer isso na lista suspensa, conforme mostrado abaixo.

    ![Obter a média da temperatura](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "Obter a média da temperatura")
8. Você também pode sobrepor um mapa de temperatura a outro para ter uma ideia melhor da diferença entre as temperaturas almejada e real. Mova o mouse para o canto do mapa da área inferior até ver a forma da alça realçada em um círculo vermelho. Arraste o mapa para o outro mapa na parte superior e solte o mouse ao ver a forma realçada no retângulo vermelho.

    ![Mesclar mapas](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "Mesclar mapas")

     A visualização de dados deve mudar para o seguinte:

    ![Visualização](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "Visualização")
9. Clique em **Salvar** para salvar a planilha. Você pode criar painéis e adicionar uma ou mais planilhas a ele.

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=Feb17_HO1-->


