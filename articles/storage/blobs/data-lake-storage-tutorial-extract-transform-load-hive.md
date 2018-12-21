---
title: 'Tutorial: Realizar operações de ETL (extração, transformação e carregamento) usando o Hive no HDInsight – Azure'
description: Saiba como extrair dados de um conjunto de dados CSV brutos, transformá-los usando o Hive no HDInsight e, em seguida, carregar os dados transformados no banco de dados SQL do Azure usando Sqoop.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 58773cfd8db5e065752e5cf89a7e6d6f172391b8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974360"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando o Apache Hive no Azure HDInsight

Neste tutorial, você pega um arquivo de dados CSV brutos, importa-o para um cluster do HDInsight e, em seguida, transforma os dados usando o Apache Hive no Azure HDInsight. Depois que os dados são transformados, você carrega esses dados em um banco de dados SQL do Azure usando o Apache Sqoop. Este artigo usa os dados da versão de pré-lançamento disponíveis publicamente.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Fazer download dos dados de voo de exemplo
> * Fazer upload de dados para um cluster do HDInsight
> * Transformar os dados usando o Hive
> * Criar uma tabela no banco de dados SQL do Azure
> * Usar Sqoop para exportar dados para o banco de dados SQL do Azure

A ilustração a seguir mostra o fluxo do aplicativo de ETL típico.

![Operação de ETL usando o Apache Hive no Azure HDInsight](./media/data-lake-storage-tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "Operação de ETL usando o Apache Hive no Azure HDInsight")

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop baseado em Linux no HDInsight**. Veja [Configurar clusters de HDInsight com Hadoop, Spark, Kafka e muito mais](./data-lake-storage-quickstart-create-connect-hdi-cluster.md) para obter as etapas sobre como criar um novo cluster HDInsight baseado em Linux.

* **Banco de dados SQL do Azure**. Você usa um banco de dados SQL do Azure como um repositório de dados de destino. Se você não tiver um Banco de Dados SQL, consulte [Criar um Banco de Dados SQL do Azure no Portal do Azure](../../sql-database/sql-database-get-started.md).

* **CLI do Azure**. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para obter mais etapas.

* **Um cliente SSH**. Para obter mais informações, consulte [Conectar ao HDInsight (Hadoop) usando SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no Azure HDInsight versão 3.4 ou posterior. Para obter mais informações, confira [baixa do HDInsight no Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Baixar os dados de voos

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Na página, selecione os valores a seguir:

   | NOME | Valor |
   | --- | --- |
   | Filtrar por ano |2013 |
   | Filtrar por período |Janeiro |
   | Campos |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Limpar todos os outros campos

3. Selecione **Baixar**. Você obtém um arquivo .zip com os campos de dados selecionados.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Fazer upload de dados para um cluster do HDInsight

Há muitas maneiras de carregar dados para o armazenamento associado a um cluster do HDInsight. Nesta seção, você deve usar o `scp` para carregar dados. Para saber mais sobre outras maneiras de carregar dados, veja [Usar o Distcp para copiar dados entre uma conta de armazenamento existente e uma nova conta de armazenamento com o Data Lake Storage Gen2 habilitado](data-lake-storage-use-distcp.md).

1. Abra um prompt de comando e use o comando a seguir para carregar o arquivo zip no nó de cabeçalho do cluster HDInsight:

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    Substitua *FILE_NAME* pelo nome do arquivo .zip. Substitua *SSH_USER_NAME* pelo logon SSH para o cluster HDInsight. Substitua *CLUSTER_NAME* pelo nome do cluster HDInsight.

   > [!NOTE]
   > Se você usar uma senha para autenticar seu logon SSH, a senha será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` e especificar a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

2. Após o upload ser concluído, conecte-se ao cluster usando SSH. Insira o seguinte comando no prompt de comando:

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. Use o comando a seguir para descompactar o arquivo .zip:

    ```bash
    unzip <FILE_NAME>.zip
    ```

    Este comando extrai um arquivo *.csv* com aproximadamente 60 MB.

4. Use os seguintes comandos para criar um diretório e copie o arquivo *.csv* para o diretório:

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Crie o sistema de arquivos do Data Lake Storage Gen2.

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformar dados usando uma consulta do Hive

Há muitas maneiras de executar um trabalho do Hive em um cluster do HDInsight. Nesta seção, você usa o Beeline para executar um trabalho do Hive. Para obter informações sobre outros métodos de execução de um trabalho do Hive, consulte [Usar Hive no HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md).

Como parte do trabalho do Hive, importe os dados do arquivo. csv em uma tabela do Hive nomeada **Delays**.

1. No prompt de SSH já existente para o cluster HDInsight, use o seguinte comando para criar e editar um novo arquivo denominado **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Use o seguinte texto como o conteúdo deste arquivo:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Para salvar o arquivo, pressione **Esc** e digite `:x`.

3. Use o seguinte comando para iniciar o Hive e executar o arquivo **flightdelays.hql**:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Após o término da execução do script __flightdelays.hql__, use o comando a seguir para abrir uma sessão interativa de Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Quando você receber o prompt do `jdbc:hive2://localhost:10001/>`, use a consulta a seguir para recuperar dados usando os dados importados de voos atrasados:

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Essa consulta recupera uma lista de cidades em que houve atrasos causados pelo clima, além do tempo médio de atrasos e a salva em `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Posteriormente, o Sqoop lê os dados desse local e os exporta para o Banco de Dados SQL do Azure.

6. Para sair do Beeline, digite `!quit` no prompt.

## <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

Esta seção pressupõe que você já criou um banco de dados SQL do Azure. Se você ainda não tem um Banco de Dados SQL, use as informações em [Criar um banco de dados SQL no Portal do Azure](../../sql-database/sql-database-get-started.md) para criar um.

Se você já tem um Banco de Dados SQL, deverá obter o nome do servidor. Para localizar o nome do servidor no [Portal do Azure](https://portal.azure.com), selecione **Bancos de Dados SQL** e, em seguida, filtre o nome do banco de dados que você deseja usar. O nome do servidor está listado na coluna **Nome do servidor**.

![Obter detalhes do Azure SQL Server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obter detalhes do Azure SQL Server")

> [!NOTE]
> Há várias maneiras de se conectar ao Banco de Dados SQL e criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.


1. Para instalar FreeTDS, utilize o seguinte comando de uma conexão SSH para o cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Após a conclusão da instalação, use o comando a seguir para conectar-se ao servidor de Banco de Dados SQL. Substitua **serverName** pelo nome do servidor do Banco de Dados SQL. Substitua **adminLogin** e **adminPassword** pelo logon do Banco de Dados SQL. Substitua **databaseName** pelo nome do banco de dados.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    Quando solicitado, insira a senha para o logon do administrador do Banco de Dados SQL.

    Você receberá saídas semelhantes ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Ao prompt `1>`, insira o seguinte:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Essa consulta cria uma tabela chamada **atrasos**, com um índice clusterizado.

    Use a seguinte consulta para verificar se a tabela foi criada:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    A saída é semelhante ao texto a seguir:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Enter `exit` at the `1>`.


## <a name="export-data-to-sql-database-using-sqoop"></a>Exportar dados para o banco de dados SQL usando Sqoop

Nas seções anteriores, você copiou os dados transformados em `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Nesta seção, você pode usar o Sqoop para exportar os dados de `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` para a tabela criada no banco de dados SQL do Azure. 

1. Use o comando a seguir para verificar se o Sqoop pode ver seu Banco de Dados SQL:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    Esse comando retorna uma lista de bancos de dados, incluindo o banco de dados no qual você criou a tabela de atrasos anteriormente.

2. Use o comando a seguir para exportar dados de hivesampletable para a tabela de atrasos:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    O Sqoop se conecta ao banco de dados que contém a tabela de atrasos e exporta dados do diretório `/tutorials/flightdelays/output` para a tabela de atrasos.

3. Depois que o comando sqoop for concluído, use o utilitário tsql para se conectar ao banco de dados:

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    Use as instruções a seguir para verificar se os dados foram exportados para a tabela de atrasos:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Você deve ver uma listagem dos dados na tabela. A tabela inclui o nome da cidade e o tempo de atraso de voo médio dessa cidade. 

    Digite `exit` para sair do utilitário tsql.

## <a name="next-steps"></a>Próximas etapas

Para aprender mais formas de trabalhar usando dados no HDInsight, consulte os seguintes artigos:

* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para Hadoop no HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver programas MapReduce de streaming do Python para o HDInsight][hdinsight-develop-streaming]
* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
