---
title: 'Tutorial: Saiba como realizar operações de extração, carregamento e transferência usando o Azure Databricks'
description: Neste tutorial, você aprenderá como extrair dados da versão prévia do Azure Data Lake Storage Gen2 para o Azure Databricks, transformar os dados e, em seguida, carregá-los no SQL Data Warehouse do Azure.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4d0ff4941405f09c2231b9cde16f4e75e2b88b4b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251666"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks

Neste tutorial, você executa uma operação de ETL (extração, transformação e carregamento de dados) usando o Azure Databricks. Extraia dados do Azure Data Lake Storage Gen2 no Azure Databricks, execute transformações nos dados no Azure Databricks e carregue os dados transformados no SQL Data Warehouse do Azure.

As etapas neste tutorial usam o conector do SQL Data Warehouse para Azure Databricks a fim de transferir dados para o Azure Databricks. Esse conector, por sua vez, usa o Armazenamento de Blobs do Azure como armazenamento temporário para os dados que estão sendo transferidos entre um cluster do Azure Databricks e o SQL Data Warehouse do Azure.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar um workspace do Azure Databricks.
> * Criar um cluster Spark no Azure Databricks.
> * Criar um sistema de arquivos e carregar dados para o Azure Data Lake Storage Gen2.
> * Crie uma entidade de serviço.
> * Extrair dados do Data Lake Storage.
> * Transformar dados no Azure Databricks.
> * Carregue os dados no SQL Data Warehouse.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

> [!div class="checklist"]
> * Crie um SQL Data Warehouse do Azure, crie uma regra de firewall no nível do servidor e conecte-se ao servidor como um administrador do servidor. Confira [Início Rápido: Criar um SQL Data Warehouse do Azure](../../sql-data-warehouse/create-data-warehouse-portal.md).
> * Crie uma chave mestra de banco de dados para o SQL Data Warehouse do Azure. Confira [Criar uma chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
> * Criar uma conta do Azure Data Lake Storage Gen2. Confira [Criar uma conta do Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
> * Crie uma conta do Armazenamento de Blobs do Azure e um contêiner dentro dela. Confira [Início Rápido: Criar uma conta de Armazenamento de Blobs do Azure](storage-quickstart-blobs-portal.md).
> * Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

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

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster Spark no Azure Databricks

1. No portal do Azure, acesse o workspace do Databricks que você criou e selecione **Iniciar Workspace**.

2. Você será redirecionado ao portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar cluster do Databricks Spark no Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Criar cluster do Databricks Spark no Azure")

4. Preencha os valores para os seguintes campos e aceite os valores padrão para os outros campos:

    * Insira um nome para o cluster.

    * Neste artigo, crie um cluster com o tempo de execução **5.1**.

    * Verifique se você marcou a caixa de seleção **Terminar depois de \_\_ minutos de inatividade**. Forneça uma duração (em minutos) para terminar o cluster quando ele não estiver sendo usado.

    * Selecione **Criar cluster**. Quando o cluster está em execução, você pode anexar notebooks a ele e executar trabalhos do Spark.

## <a name="create-a-file-system-and-upload-sample-data"></a>Criar um sistema de arquivos e carregar dados de exemplo

Primeiro, crie um sistema de arquivos em sua conta do Data Lake Storage Gen2. Em seguida, é possível carregar um arquivo de dados de exemplo no Data Lake Storage. Você pode usar esse arquivo mais tarde no Azure Databricks para executar algumas transformações.

1. Baixe o arquivo de dados de exemplo [small_radio_json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) para seu sistema de arquivos local.

2. No [portal do Azure](https://portal.azure.com/), navegue até a conta do Data Lake Storage Gen2 que você criou como um pré-requisito para este tutorial.

3. Na página **Visão geral** da conta de armazenamento, selecione **Abrir no Gerenciador**.

   ![Abrir Gerenciador de Armazenamento](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer.png "Abrir Gerenciador de Armazenamento")

4. Selecione **Abrir Gerenciador de Armazenamento do Azure** para abrir o Gerenciador de Armazenamento.

   ![Abrir segundo prompt do Gerenciador de Armazenamento](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer-2.png "Abrir segundo prompt do Gerenciador de Armazenamento")

   O Gerenciador de Armazenamento é aberto. É possível criar um sistema de arquivos e carregar os dados de exemplo usando as diretrizes neste tópico: [Início Rápido: Usar o Gerenciador de Armazenamento do Azure para gerenciar dados em uma conta do Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Crie uma entidade de serviço seguindo as diretrizes deste tópico: [Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Há algumas tarefas específicas que você precisará realizar conforme executar as etapas desse artigo.

:heavy_check_mark: Ao executar as etapas da seção [Criar um aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) do artigo, defina o campo **URL de Logon** da caixa de diálogo **Criar** como o URI do ponto de extremidade recém-coletado.

:heavy_check_mark: Ao executar as etapas da seção [Atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, atribua o aplicativo à **Função Colaborador do Armazenamento de Blobs**.

:heavy_check_mark: Ao executar as etapas da seção [Obter valores para conexão](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) do artigo, cole a ID do locatário, a ID do aplicativo e os valores de chave de autenticação em um arquivo de texto. Você precisará deles em breve.
Primeiro, você pode criar um notebook no workspace do Azure Databricks e, em seguida, executar snippets de código para criar o sistema de arquivos em sua conta de armazenamento.

## <a name="extract-data-from-the-data-lake-store"></a>Extrair dados do Data Lake Storage

Nesta seção, você cria um notebook no workspace do Azure Databricks e executa snippets de código para extrair dados do Data Lake Storage no Azure Databricks.

1. No [portal do Azure](https://portal.azure.com), acesse o workspace do Azure Databricks que você criou e selecione **Iniciar Workspace**.

2. À esquerda, selecione **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar um notebook no Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Criar notebook no Databricks")

3. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Scala** como linguagem e selecione o cluster Spark criado anteriormente.

    ![Forneça detalhes para um notebook no Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Forneça detalhes para um notebook no Databricks")

4. Selecione **Criar**.

5. Copie e cole o seguinte bloco de código na primeira célula.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

6. Nesse bloco de código, substitua os valores de espaço reservado `application-id`, `authentication-id` e `tenant-id` nesse bloco de código pelos valores coletados quando você concluiu as etapas das seções [Reservar a configuração da conta de armazenamento](#config). Substitua o valor de espaço reservado `storage-account-name` pelo nome da sua conta de armazenamento.

7. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

8. Agora é possível carregar o arquivo json de exemplo como um dataframe no Azure Databricks. Cole o código a seguir em uma nova célula. Substitua os espaços reservados mostrados entre colchetes pelos seus valores.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Substitua o valor de espaço reservado `file-system-name` pelo nome que você deu ao seu sistema de arquivos no Gerenciador de Armazenamento.

   * Substitua o espaço reservado `storage-account-name` pelo nome da sua conta de armazenamento.

9. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

10. Execute o seguinte código para ver o conteúdo do quadro de dados:

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

Os dados brutos de exemplo **small_radio_json.json** capturam o público-alvo de uma estação de rádio e contêm várias colunas. Nesta seção, você transforma os dados para recuperar somente colunas específicas do conjunto de dados.

1. Primeiro, recupere apenas as colunas **firstName**, **lastName**, **gender**, **location** e **level** do quadro de dados que você criou.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
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

2. Você pode transformar ainda mais esses dados para renomear a coluna **level** como **subscription_type**.

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados no SQL Data Warehouse do Azure

Nesta seção, você carrega os dados transformados no SQL Data Warehouse do Azure. Use o conector do SQL Data Warehouse do Azure para Azure Databricks para carregar um quadro de dados diretamente como uma tabela em um SQL Data Warehouse.

Como mencionado anteriormente, o conector do SQL Data Warehouse usa o Armazenamento de Blobs do Azure como armazenamento temporário para carregar dados entre o Azure Databricks e o SQL Data Warehouse do Azure. Portanto, comece fornecendo a configuração para se conectar à conta de armazenamento. Você já precisa ter criado a conta como parte dos pré-requisitos deste artigo.

1. Forneça a configuração para acessar a conta do Armazenamento do Azure pelo Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val authenticationKey =  "<authentication-key>"
   ```

2. Especifique uma pasta temporária a ser usada ao mover dados entre o Azure Databricks e SQL Data Warehouse do Azure.

   ```scala
   val tempDir = "wasbs://" + blob-container-name + "@" + blobStorage +"/tempDirs"
   ```

3. Execute o snippet de código a seguir para armazenar chaves de acesso do Armazenamento de Blobs do Azure na configuração. Essa ação garante que você não precise manter a chave de acesso no notebook em texto sem formatação.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, authenticationKey)
   ```

4. Forneça os valores para se conectar à instância do SQL Data Warehouse do Azure. Você precisa ter criado um SQL Data Warehouse como um pré-requisito.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Execute o snippet a seguir para carregar o quadro de dados transformado, **renamedColumnsDF**, como uma tabela em um SQL Data Warehouse. Esse snippet de código cria uma tabela chamada **SampleTable** no banco de dados SQL.

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

6. Conecte-se ao banco de dados SQL e verifique se você consegue ver um banco de dados **SampleTable**.

   ![Verificar a tabela de exemplo](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Verificar a tabela de exemplo")

7. Execute uma consulta select para verificar o conteúdo da tabela. A tabela deve ter os mesmos dados que o quadro de dados **renamedColumnsDF**.

    ![Verificar o conteúdo da tabela de exemplo](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Verificar o conteúdo da tabela de exemplo")

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, você poderá terminar o cluster. No workspace do Azure Databricks, selecione **Clusters** à esquerda. Para que o cluster seja terminado, em **Ações**, aponte para o botão de reticências (...) e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se você não terminar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha marcado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster será interrompido automaticamente se ficar inativo durante o tempo especificado.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo tutorial para saber como transmitir dados em tempo real no Azure Databricks usando os Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
>[Transmitir dados para o Azure Databricks usando Hubs de Eventos](../../azure-databricks/databricks-stream-from-eventhubs.md)
