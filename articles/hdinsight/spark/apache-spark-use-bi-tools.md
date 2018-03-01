---
title: "Spark BI usando ferramentas de visualização de dados no Azure HDInsight | Microsoft Docs"
description: "Usar ferramentas de visualização de dados para análise usando Apache Spark BI em clusters HDInsight"
keywords: "apache spark bi, spark bi, visualização de dados spark, spark business intelligence"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 97305ec6774e89e776653adbcdcf86b1cd63642f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI usando ferramentas de visualização de dados com o Azure HDInsight

Saiba como usar o [Microsoft Power BI](http://powerbi.microsoft.com) para visualizar dados em um cluster do Apache Spark no Azure HDInsight.

## <a name="prerequisites"></a>pré-requisitos

* **Concluir o artigo [Executar consultas interativas em clusters Spark no HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) e [assinatura de avaliação do Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).


## <a name="hivetable"></a>Verificar os dados

O bloco de anotações do Jupyter que você criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar uma tabela `hvac`. Essa tabela é baseada no arquivo CSV disponível em todos os clusters HDInsight Spark em **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Use o procedimento a seguir para verificar os dados.

1. A partir do notebook Jupyter, cole o código a seguir e pressione **SHIFT+ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A saída se parece com isso:

    ![Mostrar tabelas no Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Se você fechou o bloco de anotações antes de iniciar este tutorial, `hvactemptable` é limpo e, portanto, não é incluído na saída.
    Somente as tabelas do Hive que são armazenadas no metastore (indicado por **False** na coluna **isTemporary**) podem ser acessadas por meio das ferramentas de BI. Neste tutorial, você se conecta à tabela **hvac** que você criou.

2. Cole o código a seguir em uma célula vazia e pressione **SHIFT+ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A saída se parece com isso:

    ![Mostrar linhas da tabela hvac no Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. No menu **Arquivo** do notebook, clique em **Fechar e Interromper**. Feche o bloco de anotações para liberar os recursos. 















## <a name="powerbi"></a>Usar o Power BI

Nesta seção, use o Power BI para criar painéis, relatórios e visualizações a partir dos dados do cluster Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
As primeiras etapas ao trabalhar com Spark são conectar-se ao cluster no Power BI Desktop, carregar dados do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]
> O conector demonstrado neste artigo está atualmente em visualização. Forneça qualquer comentário que você tenha por meio do site da [Comunidade do Power BI](https://community.powerbi.com/) ou [Ideias do Power BI](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Abra o [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. A partir da guia **Início**, clique em **Obter Dados** e depois em **Mais**.

    ![Coloque os dados no Power BI Desktop do Apache Spark do HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Coloque os dados no Power BI do Apache Spark BI")


2. Digite `Spark` na caixa de pesquisa, selecione **Spark no HDInsight (Beta)**e, em seguida, clique em **Conectar**.

    ![Colocar dados no Power BI do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Colocar dados no Power BI do Apache Spark BI")

3. Digite a URL do cluster (no formato `mysparkcluster.azurehdinsight.net`), selecione **DirectQuery** e, em seguida, clique em **OK**.

    Você pode usar qualquer um dos modos de conectividade de dados com o Spark. Se você usar o DirectQuery, as alterações serão refletidas em relatórios sem atualizar todo o conjunto de dados. Se você importar dados, você precisará atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando usar o DirectQuery, consulte [Usando DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Insira as informações de conta de logon do HDInsight e, em seguida, clique em **Conectar**. O nome da conta padrão é *admin*.

5. Selecione a tabela `hvac`, aguarde para ver uma visualização dos dados e clique em **Carregar**.

    ![Nome de usuário e senha do cluster do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome de usuário e senha do cluster do Spark")

    O Power BI Desktop agora tem todas as informações necessárias para se conectar ao cluster do Spark e carregar os dados da tabela `hvac`. A tabela e as respectivas colunas são exibidas no painel **Campos**.  Veja a seguinte captura de tela:

6. Visualize a variação entre a temperatura almejada e a temperatura real para cada compilação: 

    1. No painel **VISUALIZAÇÕES**, selecione **Gráfico de Área**. 
    2. Arraste o campo **BuildingID** para **Eixo**e arraste os campos **ActualTemp** e **TargetTemp** para **Valor**.

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Criar visualizações de dados Spark usando o Apache Spark BI")

        O diagrama se parecerá com isso:

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Criar visualizações de dados Spark usando o Apache Spark BI")

        Por padrão, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Clique na seta para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de visualizações, você pode ver que **Sum** está selecionado.

    3. Clique nas setas para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de visualizações, selecione **Average** para obter uma média de temperatura real e temperatura alvo para cada compilação.

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Criar visualizações de dados Spark usando o Apache Spark BI")

        A sua visualização de dados deve ser semelhante àquela que aparece na captura de tela. Mova o cursor sobre a visualização para obter dicas de ferramenta com dados relevantes.

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Criar visualizações de dados Spark usando o Apache Spark BI")

7. Clique em **Arquivo**, depois em **Salvar** e digite o nome `BuildingTemperature.pbix` para o arquivo. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório para o serviço do Power BI (opcional)

O serviço do Power BI permite que você compartilhe os relatórios e painéis pela sua organização. Nesta seção, primeiro você publica o conjunto de dados e o relatório. Em seguida, você pode fixar o relatório em um painel. Painéis são normalmente usados para se concentrar em um subconjunto de dados em um relatório; você tem apenas uma visualização em seu relatório, mas ainda é útil percorrer as etapas.

1. Abra o Power BI Desktop.
2. A partir da guia **Início**, clique em **Publicar**.

    ![Publicar do Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar do Power BI Desktop")

2. Selecione um espaço de trabalho para o qual publicar seu conjunto de dados e para o qual fazer relatórios e, em seguida, clique em **Selecionar**. Na imagem a seguir, o **Meu Espaço de Trabalho** padrão está selecionado.

    ![Selecione o espaço de trabalho para o qual publicar seu conjunto de dados e para o qual fazer relatórios](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selecione o espaço de trabalho para o qual publicar seu conjunto de dados e para o qual fazer relatórios") 

3. Depois que a publicação for bem-sucedida, clique em **Abrir 'BuildingTemperature.pbix' no Power BI**.

    ![Publicação realizada com êxito, clique para inserir credenciais](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicação realizada com êxito, clique para inserir credenciais") 

4. No serviço do Power BI, clique em **Inserir credenciais**.

    ![Inserir as credenciais no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Inserir as credenciais no serviço do Power BI")

5. Clique em **Editar credenciais**.

    ![Editar as credenciais no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar as credenciais no serviço do Power BI")

6. Insira as informações de conta de logon do HDInsight e, em seguida, clique em **Entrar**. O nome da conta padrão é *admin*.

    ![Entrar no cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Entrar no cluster Spark")

7. No painel esquerdo, acesse **Espaços de trabalho** > **Meu espaço de trabalho** > **RELATÓRIOS** e, em seguida, clique em **BuildingTemperature**.

    ![Relatório listado em relatórios no painel esquerdo](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Relatório listado em relatórios no painel esquerdo")

    Você também verá a **BuildingTemperature** listada sob **DATASETS** no painel à esquerda.

    O visual criado no Power BI Desktop agora está disponível no serviço do Power BI. 

8. Focalize o cursor sobre a visualização e, em seguida, clique no ícone de fixação no canto superior direito.

    ![Relatório no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Relatório no serviço do Power BI")

9. Selecione "Novo painel", digite o nome `Building temperature` e, em seguida, clique em **Fixar**.

    ![Fixar no novo painel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Fixar no novo painel")

10. No relatório, clique em **Ir para o painel**. 

O visual é fixado no painel – você pode adicionar outros elementos visuais no relatório e fixá-los no mesmo painel. Para obter mais informações sobre relatórios e painéis, consulte [Relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>Próximas etapas

Até agora, você aprendeu como criar um cluster, criar quadros de dados do Spark para consultar dados e, em seguida, acessar esses dados de ferramentas de BI. Agora você pode examinar as instruções sobre como gerenciar os recursos de cluster e depurar os trabalhos que são executados em um cluster HDInsight Spark.

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

