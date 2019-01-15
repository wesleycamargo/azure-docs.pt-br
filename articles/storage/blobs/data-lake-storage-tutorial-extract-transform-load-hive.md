---
title: 'Tutorial: realizar operações de ETL (extração, transformação e carregamento) usando o Apache Hive no Azure HDInsight'
description: Nesse tutorial você aprende como extrair dados de um conjunto de dados CSV bruto, transformá-los usando o Apache Hive no Azure HDInsight e, em seguida, carregar os dados transformados no Banco de Dados SQL do Azure usando o Sqoop.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 65d2d69c788a54371664d1a443a79bd121332470
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105144"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrair, transformar e carregar dados usando o Apache Hive no Azure HDInsight

Neste tutorial, você executa uma operação de ETL: extrair, transformar e carregar dados. Você obtém um arquivo de dados CSV bruto, importa-o em um cluster do Azure HDInsight, transforma-o com o Apache Hive e carrega-o em um Banco de Dados SQL do Azure com o Apache Sqoop.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * extrair e carregar os dados em um cluster do HDInsight.
> * transformar os dados usando o Apache Hive.
> * Carregar os dados em um Banco de Dados SQL do Azure usando o Sqoop.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop baseado em Linux no HDInsight**: para criar um cluster do HDInsight baseado em Linux, confira [Configurar clusters no HDInsight com o Hadoop, o Spark, o Kafka e outros](./data-lake-storage-quickstart-create-connect-hdi-cluster.md).

* **Banco de Dados SQL do Azure**: Você usa um banco de dados SQL do Azure como um repositório de dados de destino. Se você não tiver um Banco de Dados SQL, consulte [Criar um Banco de Dados SQL do Azure no Portal do Azure](../../sql-database/sql-database-get-started.md).

* **CLI do Azure**: Se você ainda não instalou a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Um cliente SSH**: Para obter mais informações, confira [Conectar-se ao HDInsight (Hadoop) usando SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> As etapas neste artigo exigem um cluster do HDInsight que use o Linux. O Linux é o único sistema operacional que é usado no Azure HDInsight versão 3.4 ou posterior. Para obter mais informações, confira [baixa do HDInsight no Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="download-the-flight-data"></a>Baixar os dados de voos

Este tutorial usa dados de voo do Bureau of Transportation Statistics (Departamento de Estatísticas de Transporte dos EUA) para demonstrar como executar uma operação de ETL. Você precisa baixar esses dados para concluir o tutorial.

1. Acesse [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (Administração de pesquisa e inovação em tecnologia, Departamento de Estatísticas de Transporte dos EUA).

1. Na página, selecione os valores a seguir:

   | NOME | Valor |
   | --- | --- |
   | **Ano do Filtro** |2013 |
   | **Período do Filtro** |Janeiro |
   | **Campos** |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

1. Limpe todos os outros campos.

1. Selecione **Baixar**. Você obtém um arquivo .zip com os campos de dados selecionados.

## <a name="extract-and-upload-the-data"></a>Extrair e carregar os dados

Nesta seção, você usa o `scp` para carregar dados no cluster do HDInsight.

Abra um prompt de comando e use o comando a seguir para carregar o arquivo zip no nó de cabeçalho do cluster HDInsight:

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* Substitua \<FILE_NAME> pelo nome do arquivo .zip.
* Substitua \<SSH_USER_NAME> pelo logon SSH do cluster do HDInsight.
* Substitua \<CLUSTER_NAME> pelo nome do cluster do HDInsight.

Se você usar uma senha para autenticar seu logon SSH, a senha será solicitada. 

Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` e especificar a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

Após o upload ser concluído, conecte-se ao cluster usando SSH. Insira o seguinte comando no prompt de comando:

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

Use o comando a seguir para descompactar o arquivo .zip:

```bash
unzip <FILE_NAME>.zip
```

O comando extrai um arquivo **.csv** com aproximadamente 60 MB.

Use os seguintes comandos para criar um diretório e copie o arquivo *.csv* para o diretório:

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

Use o seguinte comando para criar o sistema de arquivos do Data Lake Storage Gen2.

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>Transformar os dados

Nesta seção, você usa o Beeline para executar um trabalho do Apache Hive.

Como parte do trabalho do Apache Hive, importe os dados do arquivo .csv para uma tabela do Apache Hive chamada **delays**.

No prompt de SSH já existente para o cluster HDInsight, use o seguinte comando para criar e editar um novo arquivo denominado **flightdelays.hql**:

```bash
nano flightdelays.hql
```

Use o seguinte texto como o conteúdo deste arquivo:

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

Para salvar o arquivo, selecione a tecla Esc e, em seguida, insira `:x`.

Use o seguinte comando para iniciar o Hive e executar o arquivo **flightdelays.hql**:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

Após o término da execução do script __flightdelays.hql__, use o comando a seguir para abrir uma sessão interativa de Beeline:

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

Quando você receber o prompt do `jdbc:hive2://localhost:10001/>`, use a consulta a seguir para recuperar dados usando os dados importados de voos atrasados:

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

Para sair do Beeline, digite `!quit` no prompt.

## <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

É necessário o nome do servidor do banco de dados SQL para essa operação. Conclua estas etapas para localizar o nome do servidor.

1. Vá para o [Portal do Azure](https://portal.azure.com).

1. Selecione **Bancos de dados SQL**.

1. Filtre pelo nome do banco de dados que você escolher usar. O nome do servidor está listado na coluna **Nome do servidor**.

1. Filtre pelo nome do banco de dados que você deseja usar. O nome do servidor está listado na coluna **Nome do servidor**.

    ![Obter detalhes do Azure SQL Server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obter detalhes do Azure SQL Server")

    Há várias maneiras de se conectar ao Banco de Dados SQL e criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.

Para instalar FreeTDS, utilize o seguinte comando de uma conexão SSH para o cluster:

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

Depois de concluir a instalação, use o seguinte comando para conectar-se ao servidor do banco de dados SQL.

* Substitua \<SERVER_NAME> pelo nome do servidor do Banco de Dados SQL.
* Substitua \<ADMIN_LOGIN> pelo logon do administrador do Banco de Dados SQL.
* Substitua \<DATABASE_NAME> pelo nome do banco de dados.

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

Quando solicitado, insira a senha para o logon de administrador do Banco de Dados SQL.

Você receberá saídas semelhantes ao seguinte texto:

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

No prompt `1>`, insira as seguintes instruções:

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas.
A consulta cria uma tabela chamada **delays**, que tem um índice clusterizado.

Use a consulta a seguir para verificar se a tabela foi criada:

```hiveql
SELECT * FROM information_schema.tables
GO
```

A saída é semelhante ao texto a seguir:

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Enter `exit` at the `1>`.

## <a name="export-and-load-the-data"></a>Exportar e carregar os dados

Nas seções anteriores, você copiou os dados transformados no local `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Nesta seção, você usa o Sqoop para exportar os dados de `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` para a tabela criada no Banco de Dados SQL do Azure.

Use o comando a seguir para verificar se o Sqoop pode ver seu Banco de Dados SQL:

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

O comando retorna uma lista de bancos de dados, incluindo o banco de dados em que você criou a tabela **delays**.

Use o seguinte comando para exportar os dados da tabela **hivesampletable** para a tabela **delays**:

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

O Sqoop conecta-se ao banco de dados que contém a tabela **delays** e exporta os dados do diretório `/tutorials/flightdelays/output` para a tabela **delays**.

Depois que o comando `sqoop` for concluído, use o utilitário tsql para conectar-se ao banco de dados:

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

Use as instruções a seguir para verificar se os dados foram exportados para a tabela **delays**:

```sql
SELECT * FROM delays
GO
```

Você deve ver uma listagem dos dados na tabela. A tabela inclui o nome da cidade e o tempo de atraso de voo médio dessa cidade.

Insira `exit` para sair do utilitário tsql.

## <a name="clean-up-resources"></a>Limpar recursos

Todos os recursos usados neste tutorial são preexistentes. Nenhuma limpeza é necessária.

## <a name="next-steps"></a>Próximas etapas

Para saber mais maneiras de trabalhar usando dados no HDInsight, confira o artigo a seguir:

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados usando o Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
