---
title: Aprenda a extrair, carregar e transferir operações usando o Azure Databricks
description: Saiba como extrair dados do Azure Data Lake Storage Gen2 Versão prévia para o Azure Databricks, transformar os dados e carregá-los no SQL Data Warehouse do Azure.
services: azure-databricks
ms.service: azure-databricks
author: jamesbak
ms.author: jamesbak
ms.reviewer: jasonwhowell
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/27/2018
ms.openlocfilehash: a224ba7030542108e521d5699e2a86f07381a1bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346535"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks

Neste tutorial, você fará uma operação de ETL (extração, transformação e carregamento de dados) para mover dados de uma conta de Armazenamento do Azure com o Azure Data Lake Storage Gen2 habilitado, para o SQL Data Warehouse do Azure usando o Azure Databricks.

A ilustração a seguir mostra o fluxo do aplicativo:

![Azure Databricks com o Data Lake Storage Gen2 e o SQL Data Warehouse](./media/handle-data-using-databricks/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks com o Data Lake Storage Gen2 e o SQL Data Warehouse")

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar um workspace do Azure Databricks
> * Criar um cluster Spark no Azure Databricks
> * Criar uma conta compatível com o Azure Data Lake Storage Gen2
> * Carregar dados no Azure Data Lake Storage Gen2
> * Criar um bloco de anotações no Azure Databricks
> * Extrair dados do Data Lake Storage Gen2
> * Transformar dados no Azure Databricks
> * Carregar dados no SQL Data Warehouse do Azure

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Crie um SQL Data Warehouse do Azure, crie uma regra de firewall no nível do servidor e conecte-se ao servidor como um administrador do servidor. Siga as instruções em [Início Rápido: Criar um SQL Data Warehouse do Azure](../../sql-data-warehouse/create-data-warehouse-portal.md)
* Crie uma chave mestra de banco de dados para o SQL Data Warehouse do Azure. Siga as instruções em [Criar uma chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Criar uma conta do Azure Data Lake Storage Gen2](quickstart-create-account.md)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

Nesta seção, você deve cria um workspace do Azure Databricks usando o Portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/handle-data-using-databricks/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço do Azure Databricks**, forneça os valores para criar um workspace do Databricks.

    ![Criar um workspace do Azure Databricks](./media/handle-data-using-databricks/create-databricks-workspace.png "Criar um workspace do Azure Databricks")

    Forneça os seguintes valores:

    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Nome do workspace**     | Forneça um nome para o seu workspace do Databricks.        |
    |**Assinatura**     | Na lista suspensa, selecione sua assinatura do Azure.        |
    |**Grupo de recursos**     | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Localidade**     | Selecione **Oeste dos EUA 2**. Para outras regiões disponíveis, consulte [serviços do Azure por região](https://azure.microsoft.com/regions/services/).        |
    |**Tipo de preço**     |  Escolha entre o cluster **Standard** e o **Premium**. Para saber mais sobre essas camadas, confira [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selecione **Fixar no painel** e depois **Criar**.

3. A criação da conta leva alguns minutos. Durante a criação da conta, o portal exibe o bloco **Enviando a implantação para o Azure Databricks** no lado direito. Talvez seja necessário rolar diretamente no painel para ver o bloco. Também é exibida na parte superior da tela de uma barra de progresso. Você pode assistir a área de andamento.

    ![Bloco de implantação do Databricks](./media/handle-data-using-databricks/databricks-deployment-tile.png "Bloco de implantação do Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster Spark no Databricks

1. No portal do Azure, vá para o workspace do Databricks que você criou e selecione **Inicializar Workspace**.

2. Você é redirecionado para o portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/handle-data-using-databricks/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar cluster do Databricks Spark no Azure](./media/handle-data-using-databricks/create-databricks-spark-cluster.png "Criar cluster do Databricks Spark no Azure")

    Preencha os valores para os seguintes campos e aceite os valores padrão para os outros campos:

    * Insira um nome para o cluster.
    * Neste artigo, crie um cluster com o tempo de execução **4.2**.
    * Verifique se você marcou a caixa de seleção **Terminar depois de \_\_ minutos de inatividade**. Forneça uma duração (em minutos) para encerrar o cluster caso ele não esteja sendo usado.

    Selecione **Criar cluster**. Quando o cluster está em execução, você pode anexar blocos de notas a ele e executar trabalhos do Spark.

## <a name="create-storage-account-file-system"></a>Criar sistema de arquivos da conta de armazenamento

Nesta seção, você cria um bloco de anotações no workspace do Azure Databricks e executa snippets de código para configurar a conta de armazenamento.

1. No [Portal do Azure](https://portal.azure.com), vá para o workspace do Azure Databricks que você criou e selecione **Inicializar Workspace**.

2. No painel esquerdo, escolha **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar bloco de notas em Databricks](./media/handle-data-using-databricks/databricks-create-notebook.png "Criar bloco de notas em Databricks")

3. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Scala** como linguagem e selecione o cluster Spark criado anteriormente.

    ![Criar bloco de notas em Databricks](./media/handle-data-using-databricks/databricks-notebook-details.png "Criar bloco de notas em Databricks")

    Selecione **Criar**.

4. Insira o código a seguir na primeira célula e execute o código:

    ```scala
    spark.conf.set("fs.azure.account.key.<ACCOUNT_NAME>.dfs.core.windows.net", "<ACCOUNT_KEY>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false") 
    ```

    Pressione **SHIFT + ENTER** para executar a célula de código.

    Agora o sistema de arquivos foi criado para a conta de armazenamento.

## <a name="upload-data-to-the-storage-account"></a>Carregue os dados na conta de armazenamento

A próxima etapa é carregar um arquivo de dados de exemplo na conta de armazenamento para transformar posteriormente no Azure Databricks. 

> [!NOTE]
> Se você ainda não tiver uma conta do Data Lake Storage Gen2 funcional, siga o [início rápido para criar uma](./quickstart-create-account.md).

1. Faça o download de (**small_radio_json**) do repositório [Exemplos do U-SQL e rastreamento de problema](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) e anote o caminho onde você salvou o arquivo.

2. Em seguida, carregue os dados de exemplo em sua conta de armazenamento. O método usado para carregar dados em sua conta de armazenamento difere dependendo se você tem ou não o namespace hierárquico habilitado.

    Se o namespace hierárquico estiver habilitado na sua conta do Armazenamento do Azure, use o Azure Data Factory, distp ou AzCopy (versão 10) para cuidar do carregamento. AzCopy versão 10 só está disponível para versão prévia neste atualmente. Para usar o AzCopy, cole o código a seguir em uma janela de comando:

    ```bash
    set ACCOUNT_NAME=<ACCOUNT_NAME>
    set ACCOUNT_KEY=<ACCOUNT_KEY>
    azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
    ```
    
## <a name="extract-data-from-azure-storage"></a>Extrair dados do Armazenamento do Azure

Retorne ao bloco de anotações do DataBricks e insira o código a seguir em uma nova célula:

1. Adicione o snippet de código a seguir em uma célula de código vazia e substitua os valores do espaço reservado pelos valores que você salvou anteriormente da conta de armazenamento.

    ```scala
    dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
    dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
    ```

    Pressione **SHIFT + ENTER** para executar a célula de código.

2. Agora você pode carregar o arquivo json de exemplo como um dataframe no Azure Databricks. Cole o código a seguir em uma nova célula e pressione **SHIFT+ENTER** (certificando-se de substituir os valores do espaço reservado):

    ```scala
    val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
    ```

3. Execute o código a seguir para ver o conteúdo do dataframe.

    ```scala
    df.show()
    ```

    Você verá um resultado semelhante ao seguinte snippet:

    ```bash
    +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
    |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
    +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
    | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
    | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
    | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
    ...
    ...
    ```

Você extraiu os dados do Azure Data Lake Storage Gen2 para o Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformar dados no Azure Databricks

Os dados de exemplo brutos **small_radio_json.json** capturam o público de uma estação de rádio e têm várias colunas. Nesta seção, você transforma os dados para recuperar somente colunas específicas do conjunto de dados.

1. Comece recuperando apenas as colunas *firstName*, *lastName*, *gender*, *location* e *level* do dataframe já criado.

    ```scala
    val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
    ```

    Você obterá um resultado do tipo mostrado no seguinte snippet de código:

    ```bash
    +---------+----------+------+--------------------+-----+
    |firstname|  lastname|gender|            location|level|
    +---------+----------+------+--------------------+-----+
    | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |     Tess|  Townsend|     F|Nashville-Davidso...| free|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
    |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Tess|  Townsend|     F|Nashville-Davidso...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    +---------+----------+------+--------------------+-----+
    ```

2.  Você pode transformar ainda mais esses dados para renomear a coluna **level** como **subscription_type**.

    ```scala
    val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
    renamedColumnsDF.show()
    ```

    Você obterá uma saída do tipo mostrado no snippet de código a seguir.

    ```bash
    +---------+----------+------+--------------------+-----------------+
    |firstname|  lastname|gender|            location|subscription_type|
    +---------+----------+------+--------------------+-----------------+
    | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
    |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    +---------+----------+------+--------------------+-----------------+
    ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados no SQL Data Warehouse do Azure

Nesta seção, você carrega os dados transformados no SQL Data Warehouse do Azure. Usando o conector do SQL Data Warehouse do Azure para o Azure Databricks, você pode carregar um dataframe diretamente como uma tabela no SQL Data Warehouse.

Como mencionado anteriormente, o conector do SQL Data Warehouse usa o Armazenamento de Blobs do Azure como um armazenamento temporário para carregar dados entre o Azure Databricks e o SQL Data Warehouse do Azure. Portanto, comece fornecendo a configuração para se conectar à conta de armazenamento. Você já deve ter criado a conta como parte dos pré-requisitos para este artigo.

1. Forneça a configuração para acessar a conta do Armazenamento do Azure pelo Azure Databricks.

    ```scala
    val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
    val fileSystemName = "<FILE_SYSTEM_NAME>"
    val accessKey =  "<ACCESS_KEY>"
    ```

2. Especifique uma pasta temporária que será usada para mover dados entre o Azure Databricks e o SQL Data Warehouse do Azure.

    ```scala
    val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
    ```

3. Execute o snippet de código a seguir para armazenar chaves de acesso do Armazenamento de Blobs do Azure na configuração. Isso faz com que você não precise manter a chave de acesso no bloco de anotações em texto sem formatação.

    ```scala
    val acntInfo = "fs.azure.account.key."+ storageURI
    sc.hadoopConfiguration.set(acntInfo, accessKey)
    ```

4. Forneça os valores para se conectar à instância do SQL Data Warehouse do Azure. Você deve ter criado um SQL Data Warehouse como parte dos pré-requisitos.

    ```scala
    //SQL Data Warehouse related settings
    val dwDatabase = "<DATABASE NAME>"
    val dwServer = "<DATABASE SERVER NAME>" 
    val dwUser = "<USER NAME>"
    val dwPass = "<PASSWORD>"
    val dwJdbcPort =  "1433"
    val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
    val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
    val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
    ```

5. Execute o snippet de código a seguir para carregar o dataframe transformado, **renamedColumnsDF**, como uma tabela no SQL Data Warehouse. Esse snippet de código cria uma tabela chamada **SampleTable** no banco de dados SQL.

    ```scala
    spark.conf.set(
        "spark.sql.parquet.writeLegacyFormat",
        "true")
    
    renamedColumnsDF.write
        .format("com.databricks.spark.sqldw")
        .option("url", sqlDwUrlSmall) 
        .option("dbtable", "SampleTable")
        .option( "forward_spark_azure_storage_credentials","True")
        .option("tempdir", tempDir)
        .mode("overwrite")
        .save()
    ```

6. Conecte-se ao banco de dados SQL e verifique se você consegue ver uma **SampleTable**.

    ![Verificar tabela de exemplo](./media/handle-data-using-databricks/verify-sample-table.png "Verificar tabela de exemplo")

7. Execute uma consulta select para verificar o conteúdo da tabela. Ela deve ter os mesmos dados que o dataframe **renamedColumnsDF**.

    ![Verificar conteúdo da tabela de exemplo](./media/handle-data-using-databricks/verify-sample-table-content.png "Verificar conteúdo da tabela de exemplo")

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar de executar o tutorial, você poderá encerrar o cluster. Para isso, no workspace do Azure Databricks, no painel esquerdo, selecione **Clusters**. No cluster que deseja encerrar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/handle-data-using-databricks/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se você não encerrar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha selecionado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster será interrompido automaticamente se ficar inativo durante o tempo especificado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um workspace do Azure Databricks
> * Criar um cluster Spark no Azure Databricks
> * Criar uma conta compatível com o Azure Data Lake Storage Gen2
> * Carregar dados no Azure Data Lake Storage Gen2
> * Criar um bloco de anotações no Azure Databricks
> * Extrair dados do Data Lake Storage Gen2
> * Transformar dados no Azure Databricks
> * Carregar dados no SQL Data Warehouse do Azure

Avance para o próximo tutorial a fim de saber mais sobre o fluxo de dados em tempo real no Azure Databricks usando os Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
>[Transmitir dados para o Azure Databricks usando Hubs de Eventos](../../azure-databricks/databricks-stream-from-eventhubs.md)
