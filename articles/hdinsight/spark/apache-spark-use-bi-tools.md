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
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 18f495864befafd26e7adafb5c01612222d2cfdf
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI usando ferramentas de visualização de dados com o Azure HDInsight

Saiba como usar o [Microsoft Power BI](http://powerbi.microsoft.com) e o [Tableau](http://www.tableau.com) para visualizar dados em um cluster do Apache Spark no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* **Conclua [Executar consultas interativas em um cluster Spark no HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) e [assinatura de avaliação do Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).
* **Tableau**: [Tableau Desktop](http://www.tableau.com/products/desktop) e [Driver ODBC Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).


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

    O Power BI Desktop agora tem todas as informações necessárias para se conectar ao cluster do Spark e carregar os dados da tabela `hvac`. A tabela e as respectivas colunas são exibidas no painel **Campos**.  Consulte a seguinte captura de tela.

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

## <a name="tableau"></a>Use o Tableau Desktop 

> [!NOTE]
> Esta seção é aplicável somente para clusters do Spark 1.5.2 criados no Azure HDInsight.
>
>

1. Instale o [Tableau Desktop](http://www.tableau.com/products/desktop) no computador em que você está executando este tutorial do Apache Spark BI.

2. Verifique se o computador também tem o driver ODBC do Microsoft Spark instalado. Você pode instalar o driver clicando [aqui](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Inicie o Tableau Desktop. No painel esquerdo, na lista de servidores aos quais se conectar, clique em **Spark SQL**. Se Spark SQL não estiver listado por padrão no painel esquerdo, você poderá encontrá-lo ao clicar em **Mais Servidores**.
2. Na caixa de diálogo de conexão do Spark SQL, forneça os valores conforme mostrado na captura de tela e clique em **OK**.

    ![Conectar-se a um cluster do Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Conectar-se a um cluster do Apache Spark BI")

    O menu suspenso de autenticação listará o **Serviço Microsoft Azure HDInsight** como uma opção somente se você tiver instalado o [Driver ODBC do Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) no computador.
3. Na próxima tela, no menu suspenso **Esquema**, clique no ícone **Localizar** e clique em **padrão**.

    ![Localizar o esquema para o Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Localizar o esquema para o Apache Spark BI")
4. Para o campo **Tabela**, clique no ícone **Localizar** novamente para listar todas as tabelas Hive disponíveis no cluster. Você deve ver a tabela **hvac** criada anteriormente usando o bloco de anotações.

    ![Localizar tabela para o Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Localizar a tabela para o Apache Spark BI")
5. Arraste e solte a tabela para a caixa superior à direita. O Tableau importa os dados e exibe o esquema conforme destacado pela caixa vermelha.

    ![Adicionar tabelas ao Tableau para o Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Adicionar tabelas ao Tableau para o Apache Spark BI")
6. Clique na guia **Sheet1** na parte inferior esquerda. Faça uma visualização que mostre as temperaturas almejada e real para todos os edifícios para cada data. Arraste **Data** e **ID do Prédio** para **Colunas** e **Temp. real**/**Temp. almejada** para **Linhas**. Em **Marcas**, selecione **Área** para usar uma mapa de área para a visualização de dados do Spark.

     ![Adicionar campos para visualização de dados do Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Adicionar campos para visualização de dados do Spark")
7. Por padrão, os campos de temperatura são mostrados em agregado. Se você desejar mostrar a temperatura média em vez disso, você pode fazer isso a partir da lista suspensa, conforme mostrado nas capturas de tela abaixo.

    ![Obter a temperatura média para visualização de dados do Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Obter a temperatura média para visualização de dados do Spark")

8. Você também pode sobrepor um mapa de temperatura a outro para ter uma ideia melhor da diferença entre as temperaturas almejada e real. Mova o mouse para o canto do mapa da área inferior até ver a forma da alça realçada em um círculo vermelho. Arraste o mapa para o outro mapa na parte superior e solte o mouse ao ver a forma realçada no retângulo vermelho.

    ![Mesclar mapas para visualização de dados do Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Mesclar mapas para visualização de dados do Spark")

     A visualização de dados deve ser alterada conforme mostrado na captura de tela:

    ![Saída do Tableau para visualização de dados do Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Saída do Tableau para visualização de dados do Spark")
9. Clique em **Salvar** para salvar a planilha. Você pode criar painéis e adicionar uma ou mais planilhas a ele.

## <a name="next-steps"></a>Próximas etapas

Até agora, você aprendeu como criar um cluster, criar quadros de dados do Spark para consultar dados e, em seguida, acessar esses dados de ferramentas de BI. Agora você pode examinar as instruções sobre como gerenciar os recursos de cluster e depurar os trabalhos que são executados em um cluster HDInsight Spark.

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

