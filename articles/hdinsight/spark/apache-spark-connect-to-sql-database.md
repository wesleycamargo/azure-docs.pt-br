---
title: "Use o Apache Spark para leitura e gravação dados no Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como configurar uma conexão entre o cluster do Azure HDInsight Spark e um Banco de Dados SQL do Azure para ler dados, gravar dados e transmitir dados em um Banco de Dados SQL"
services: hdinsight
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: nitinme
ms.openlocfilehash: 28ed6b9774bb85c7ec806c7775c34f8bc3d66bde
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usar o cluster do Azure HDInsight Spark para leitura e gravação dos dados no Banco de Dados SQL do Azure

Saiba como conectar um cluster Apache Spark no Microsoft Azure HDInsight com um Banco de Dados SQL do Azure e, em seguida, ler, gravar e transmitir dados no Banco de Dados SQL. As instruções contidas neste artigo usam um Notebook Jupyter para executar os trechos de código Scala. No entanto, você pode criar um aplicativo autônomo em Scala ou Python e realizar as mesmas tarefas. 

## <a name="prerequisites"></a>pré-requisitos

* **Cluster do Azure HDInsight Spark**.  Siga as instruções em [Criar um cluster do Apache Spark no HDInsight ](apache-spark-jupyter-spark-sql.md).

* **Banco de Dados SQL do Azure**. Siga as instruções em [Criar um Banco de Dados SQL do Azure](../../sql-database/sql-database-get-started-portal.md). Certifique-se de criar um banco de dados com o esquema e dados **AdventureWorksLT** de exemplo. Além disso, certifique-se de criar uma regra de firewall no nível do servidor para permitir que o endereço IP do seu cliente acesse o banco de dados SQL no servidor. As instruções para adicionar a regra de firewall estão disponíveis no mesmo artigo. Após criar seu Banco de Dados SQL do Azure, certifique-se de manter os seguintes valores a acessíveis. Esses valores serão necessários para se conectar ao banco de dados de um cluster Spark.

    * Nome do servidor que hospeda o Banco de Dados SQL do Azure
    * Nome do banco de dados SQL do Azure
    * Nome/senha de usuário administrador do Banco de Dados SQL do Azure

* **SQL Server Management Studio**. Siga as instruções em [Usar SSMS para conectar e consultar dados](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Criar um notebook Jupyter

Inicie criando um notebook Jupyter associado ao cluster Spark. Você usa esse notebook para executar os trechos de código usados neste artigo. 

1. Do [portal do Azure](https://portal.azure.com/), abra o seu cluster. 

2. A partir da seção **Links rápidos**, clique em **Painéis do Cluster** para abrir o modo de exibição dos **Painéis do Cluster**.  Se você não vir **Links rápidos**, clique em **visão geral** no menu à esquerda na folha.

    ![Painel do cluster no Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "Painel do cluster no Spark") 

3. Clique em **Notebook Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

    ![Bloco de anotações do Jupyter no Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Bloco de anotações do Jupyter no Spark")
   
   > [!NOTE]
   > Também é possível acessar o notebook Jupyter no cluster Spark, abrindo a seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. No notebook Jupyter, no canto superior direito, clique em **Novo**e, em seguida, clique em **Spark** para criar um notebook Scala. Os notebooks Jupyter no cluster do Azure HDInsight Spark também fornecem o kernel **PySpark** para aplicativos Python2 e o kernel **PySpark3** para aplicativos Python3. Para este artigo, criamos um notebook Scala.
   
    ![Kernels para bloco de anotações do Jupyter no Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels para bloco de anotações do Jupyter no Spark")

    Para saber mais sobre os três kernels, consulte [clusters kernels de blocos de anotações do Jupyter de uso com o Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > Neste artigo, usamos um kernel (Scala) Spark porque atualmente os dados de streaming do Spark para banco de dados SQL têm suporte apenas em Scala e Java. Embora a leitura e gravação em SQL possam ser feitas usando o Python, visando manter a consistência neste artigo nós usamos o Scala para todas as três operações.
   >

5. Isso abre um novo notebook com um nome padrão, **Sem título**. Clique no nome do notebook e insira o nome de sua escolha.

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Fornecer um nome para o bloco de anotações")

Agora, você pode iniciar a criação do seu aplicativo.
    
## <a name="read-data-from-azure-sql-database"></a>Leitura dos dados do Banco de Dados SQL do Azure

Nesta seção, você faz a leitura dos dados de uma tabela (por exemplo, **SalesLT.Address**) que existe no banco de dados AdventureWorks.

1. Em um novo notebook Jupyter, em uma célula de código, cole o seguinte trecho de código e substitua os valores de espaço reservados pelos valores para seu Banco de Dados SQL do Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Pressione **SHIFT + ENTER** para executar a célula de código.  

2. O trecho de código a seguir compila uma URL do JDBC que você pode informar para as APIs do dataframe do Spark que criam um objeto`Properties` para manter os parâmetros. Cole o trecho de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. O trecho de código a seguir cria um dataframe com os dados de uma tabela no seu Banco de Dados SQL do Azure. Neste trecho de dados, usamos uma tabela **SalesLT.Address** que está disponível como parte do banco de dados **AdventureWorksLT**. Cole o trecho de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Agora, é possível executar operações no dataframe, como obter o esquema de dados:

       sqlTableDF.printSchema
   
    Você verá uma saída semelhante à seguinte:

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Fornecer um nome para o bloco de anotações")

5. Também é possível executar operações como, por exemplo, recuperar as 10 melhores filas.

       sqlTableDF.show(10)

6. Ou, recuperar colunas específicas do conjunto de dados.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Gravação de dados no Banco de Dados SQL do Azure

Nesta seção, usamos um exemplo CSV de arquivo disponível no cluster para criar uma tabela no Banco de Dados SQL do Azure e preenchê-lo com dados. O arquivo CSV de exemplo (**HVAC.csv**) está disponível em todos os clusters HDInsight em `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Em um novo notebook Jupyter, em uma célula de código, cole o seguinte trecho de código e substitua os valores de espaço reservados pelos valores para seu Banco de Dados SQL do Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Pressione **SHIFT + ENTER** para executar a célula de código.  

2. O trecho de código a seguir compila uma URL do JDBC que você pode informar para as APIs do dataframe do Spark que criam um objeto`Properties` para manter os parâmetros. Cole o trecho de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. O trecho de código a seguir extrai o esquema dos dados em HVAC.csv e usa o esquema para carregar os dados do CSV em um dataframe, `readDf`. Cole o trecho de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Use o dataframe `readDf` para criar uma tabela temporária, `temphvactable`. Em seguida, use a tabela temporária para criar uma tabela de hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Finalmente, use a tabela de hive para criar uma tabela no Banco de Dados SQL do Azure. O trecho de código a seguir cria `hvactable` no Banco de Dados SQL do Azure.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Conecte-se ao Banco de Dados SQL do Azure usando SSMS e verifique se há um `dbo.hvactable`.

    a. Inicie o SSMS e conecte-se ao Banco de Dados SQL do Azure, fornecendo os detalhes de conexão como mostrado na captura de tela abaixo.

    ![Conectar-se ao Banco de Dados SQL usando SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Conectar-se ao Banco de Dados SQL usando SSMS")

    b. No Pesquisador de Objetos, expanda o Banco de Dados SQL do Azure e o nó da Tabela para ver o **dbo.hvactable** criado.

    ![Conectar-se ao Banco de Dados SQL usando SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Conectar-se ao Banco de Dados SQL SSMS")

## <a name="stream-data-into-azure-sql-database"></a>Transmitir dados no Banco de Dados SQL do Azure

Nesta seção, transmitimos dados para o **hvactable** já criados no Banco de Dados SQL do Azure.

1. Como primeira etapa, verifique se não há registros no **hvactable**. Usando SSMS, execute a seguinte consulta na tabela.

       DELETE FROM [dbo].[hvactable]

2. Crie um novo notebook Jupyter no cluster do Azure HDInsight Spark. Em uma célula de código, cole o seguinte trecho de código e, em seguida, pressione **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Nós transmitimos dados do **HVAC.csv** no hvactable. O arquivo HVAC.csv está disponível no cluster em */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. No trecho de código a seguir, primeiro recebemos o esquema dos dados a serem transmitidos. Em seguida, criamos um dataframe de transmissão usando esse esquema. Cole o trecho de código a seguir em uma célula de código e pressione **SHIFT+ENTER** para executar.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema1).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. A saída mostra o esquema de **HVAC.csv**. O **hvactable** também tem o mesmo esquema. A saída lista as colunas na tabela.

    ![Esquema da tabela](./media/apache-spark-connect-to-sql-database/schema-of-table.png "Esquema da tabela")

5. Finalmente, use o seguinte trecho de código para a leitura dos dados do HVAC.csv e transmita-o para o **hvactable** no Banco de Dados SQL do Azure. Cole o trecho de código em uma célula de código, substitua os valores de espaço reservado pelos valores do seu Banco de Dados SQL do Azure e, em seguida, pressione **SHIFT + ENTER** para executar.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

6. Verifique se os dados estão sendo transmitidos para o **hvactable** executando a seguinte consulta. Sempre que você executar a consulta, o número de linhas na tabela aumentando será exibido.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Próximas etapas

* [Usar o cluster do Azure HDInsight Spark para analisar dados na Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Processo de eventos de fluxo estruturado usando EventHub](apache-spark-eventhub-structured-streaming.md)
* [Usar o fluxo estruturado do Spark com Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
