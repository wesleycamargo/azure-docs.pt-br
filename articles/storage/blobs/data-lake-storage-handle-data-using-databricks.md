---
title: 'Tutorial: Saiba como realizar operações de extração, carregamento e transferência usando o Azure Databricks'
description: Neste tutorial, você aprenderá como extrair dados da versão prévia do Azure Data Lake Storage Gen2 para o Azure Databricks, transformar os dados e, em seguida, carregá-los no SQL Data Warehouse do Azure.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107082"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks

Neste tutorial, você usa o Azure Databricks para executar uma operação de ETL (extração, transformação e carregamento de dados). Você pode mover dados de uma conta de Armazenamento do Azure com o Azure Data Lake Storage Gen2 habilitado para o SQL Data Warehouse do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um workspace do Azure Databricks.
> * Criar um cluster Spark no Azure Databricks.
> * Criar uma conta compatível com o Azure Data Lake Storage Gen2.
> * Carregar dados no Azure Data Lake Storage Gen2.
> * Criar um notebook no Azure Databricks.
> * Extrair dados do Data Lake Storage Gen2.
> * Transformar dados no Azure Databricks.
> * Carregue os dados no SQL Data Warehouse.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Crie um SQL Data Warehouse do Azure, crie uma regra de firewall no nível do servidor e conecte-se ao servidor como um administrador do servidor. Siga as instruções no artigo [Início rápido: Criar um SQL Data Warehouse do Azure](../../sql-data-warehouse/create-data-warehouse-portal.md).
* Crie uma chave mestra de banco de dados para o SQL Data Warehouse do Azure. Siga as instruções no artigo [Criar uma chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Crie uma conta do Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* Faça o download de (**small_radio_json**) do repositório [Exemplos do U-SQL e rastreamento de problema](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) e anote o caminho onde você salvou o arquivo.
* Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-the-workspace"></a>Criar o workspace

Nesta seção, você cria um workspace do Azure Databricks usando o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks no portal do Azure")

1. Em **Serviço do Azure Databricks**, forneça os valores a seguir para criar um workspace do Databricks:

    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Nome do workspace**     | Forneça um nome para o seu workspace do Databricks.        |
    |**Assinatura**     | Na lista suspensa, selecione sua assinatura do Azure.        |
    |**Grupo de recursos**     | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Localidade**     | Selecione **Oeste dos EUA 2**.        |
    |**Tipo de preço**     |  Selecione **Padrão**.     |

    ![Criar um workspace do Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Criar um workspace do Azure Databricks")

1. Selecione **Fixar no painel** e depois **Criar**.

1. A criação da conta leva alguns minutos. Durante a criação da conta, o portal exibirá o bloco **Enviando a implantação para o Azure Databricks** no lado direito. Para monitorar o status da operação, veja a barra de progresso na parte superior.

    ![Bloco de implantação do Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Bloco de implantação do Databricks")

## <a name="create-the-spark-cluster"></a>Criar o cluster Spark

Para executar as operações neste tutorial, você precisa de um cluster Spark. Siga as seguintes etapas para criar o cluster Spark.

1. No portal do Azure, acesse o workspace do Databricks que você criou e selecione **Iniciar Workspace**.

1. Você será redirecionado ao portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks no Azure")

1. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar cluster do Databricks Spark no Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Criar cluster do Databricks Spark no Azure")

1. Preencha os valores para os seguintes campos e aceite os valores padrão para os outros campos:

    * Insira um nome para o cluster.
    * Neste artigo, crie um cluster com o tempo de execução **5.1**.
    * Verifique se você marcou a caixa de seleção **Terminar depois de \_\_ minutos de inatividade**. Forneça uma duração (em minutos) para terminar o cluster quando ele não estiver sendo usado.

1. Selecione **Criar cluster**.

Quando o cluster está em execução, você pode anexar notebooks a ele e executar trabalhos do Spark.

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Para armazenar dados em sua conta de armazenamento do Azure Data Lake Storage Gen2, você precisará criar um sistema de arquivos.

Primeiro, você pode criar um notebook no workspace do Azure Databricks e, em seguida, executar snippets de código para criar o sistema de arquivos em sua conta de armazenamento.

1. No [portal do Azure](https://portal.azure.com), acesse o workspace do Azure Databricks que você criou e selecione **Iniciar Workspace**.

1. À esquerda, selecione **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar um notebook no Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Criar notebook no Databricks")

1. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Scala** como linguagem e selecione o cluster Spark criado anteriormente.

    ![Forneça detalhes para um notebook no Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Forneça detalhes para um notebook no Databricks")

    Selecione **Criar**.

1. Insira o código a seguir na primeira célula do notebook e execute o código. Substitua os espaços reservados mostrados entre colchetes no exemplo pelos seus próprios valores:

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

1. Selecione as teclas Shift + Enter para executar o código.

Agora o sistema de arquivos foi criado para a conta de armazenamento.

## <a name="upload-the-sample-data"></a>Carregar os dados de exemplo

A próxima etapa é carregar um arquivo de dados de exemplo na conta de armazenamento a ser transformado mais tarde no Azure Databricks.

Carregue os dados de exemplo que você baixou na conta de armazenamento. O método usado para carregar dados na conta de armazenamento difere dependendo se o namespace hierárquico está habilitado ou não.

Você pode usar o Azure Data Factory, o distp ou o AzCopy (versão 10) para carregar. O AzCopy versão 10 está disponível atualmente apenas por meio de versão prévia. Para usar o AzCopy, cole o código a seguir em uma janela de comando:

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Extrair os dados

Para trabalhar com os dados de exemplo no Databricks, você precisará extrair os dados da conta de armazenamento.

Volte para o notebook do Databricks e insira o código a seguir em uma nova célula no notebook.

Adicione o snippet a seguir em uma célula de código vazia. Substitua os espaços reservados mostrados entre colchetes pelos valores que você já salvou da conta de armazenamento.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Selecione as teclas Shift + Enter para executar o código.

Agora você pode carregar o arquivo json de exemplo como um dataframe no Azure Databricks. Cole o código a seguir em uma nova célula. Substitua os espaços reservados mostrados entre colchetes pelos seus valores.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Selecione as teclas Shift + Enter para executar o código.

Execute o seguinte código para ver o conteúdo do quadro de dados:

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

## <a name="transform-the-data"></a>Transformar os dados

Os dados brutos de exemplo **small_radio_json.json** capturam o público-alvo de uma estação de rádio e contêm várias colunas. Nesta seção, você transforma os dados para recuperar somente colunas específicas do conjunto de dados.

Primeiro, recupere apenas as colunas **firstName**, **lastName**, **gender**, **location** e **level** do quadro de dados que você criou.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

Você receberá uma saída como a mostrada no snippet a seguir:

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

Você pode transformar ainda mais esses dados para renomear a coluna **level** como **subscription_type**.

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

Você receberá uma saída como a mostrada no snippet a seguir.

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

## <a name="load-the-data"></a>Carregar os dados

Nesta seção, você carrega os dados transformados no SQL Data Warehouse do Azure. Use o conector do SQL Data Warehouse do Azure para Azure Databricks para carregar um quadro de dados diretamente como uma tabela em um SQL Data Warehouse.

O conector do SQL Data Warehouse usa o Armazenamento de Blobs do Azure como um armazenamento temporário para carregar dados entre o Azure Databricks e o SQL Data Warehouse do Azure. Portanto, comece fornecendo a configuração para se conectar à conta de armazenamento. Você já precisa ter criado a conta como parte dos pré-requisitos deste artigo.

Forneça a configuração para acessar a conta do Armazenamento do Azure pelo Azure Databricks.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Especifique uma pasta temporária a ser usada ao mover dados entre o Azure Databricks e SQL Data Warehouse do Azure.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Execute o snippet de código a seguir para armazenar chaves de acesso do Armazenamento de Blobs do Azure na configuração. Essa ação garante que você não precise manter a chave de acesso no notebook em texto sem formatação.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Forneça os valores para se conectar à instância do SQL Data Warehouse do Azure. Você precisa ter criado um SQL Data Warehouse como um pré-requisito.

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

Execute o snippet a seguir para carregar o quadro de dados transformado, **renamedColumnsDF**, como uma tabela em um SQL Data Warehouse. Esse snippet de código cria uma tabela chamada **SampleTable** no banco de dados SQL.

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

Conecte-se ao banco de dados SQL e verifique se você consegue ver um banco de dados **SampleTable**.

![Verificar a tabela de exemplo](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Verificar a tabela de exemplo")

Execute uma consulta select para verificar o conteúdo da tabela. A tabela deve ter os mesmos dados que o quadro de dados **renamedColumnsDF**.

![Verificar o conteúdo da tabela de exemplo](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Verificar o conteúdo da tabela de exemplo")

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, você poderá terminar o cluster. No workspace do Azure Databricks, selecione **Clusters** à esquerda. Para que o cluster seja terminado, em **Ações**, aponte para o botão de reticências (...) e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se você não terminar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha marcado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster será interrompido automaticamente se ficar inativo durante o tempo especificado.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo tutorial para saber como transmitir dados em tempo real no Azure Databricks usando os Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
>[Transmitir dados para o Azure Databricks usando Hubs de Eventos](../../azure-databricks/databricks-stream-from-eventhubs.md)
