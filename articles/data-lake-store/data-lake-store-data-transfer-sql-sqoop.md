<properties 
   pageTitle="Copiar dados entre o Repositório Data Lake e o banco de dados SQL do Azure usando o Sqoop | Microsoft Azure"
   description="Usar o Sqoop para copiar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/29/2016"
   ms.author="nitinme"/>

# Copiar dados entre o Repositório Data Lake e o banco de dados SQL do Azure usando o Sqoop

Saiba como usar o Apache Sqoop para importar e exportar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake.
 

## O que é o Sqoop?

Os aplicativos de big data são uma opção natural para o processamento de dados semi-estruturados e não estruturados, como logs e arquivos. No entanto, também pode ser necessário processar dados estruturados armazenados em bancos de dados relacionais.

O [Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e um repositório de big data, como o Repositório Data Lake. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacionais), como o Banco de Dados SQL do Azure no Repositório Data Lake. E, em seguida, transformar e analisar os dados usando cargas de trabalho de big data e exportar os dados de volta para um RDBMS. Neste tutorial, você usa um Banco de Dados SQL do Azure como seu banco de dados relacional para importação/exportação.
 

## Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup). 
- **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que você tenha um cluster HDInsight Linux com acesso ao Repositório Data Lake.
- **Banco de dados SQL do Azure**. Para saber mais sobre como criar um, confira [Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started.md)

## Criar tabelas de exemplo no Banco de Dados SQL do Azure

1. Para começar, crie duas tabelas de exemplo no Banco de Dados SQL do Azure. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para se conectar ao Banco de Dados SQL do Azure e então execute as consultas a seguir.

	**Criar Tabela1**

		CREATE TABLE [dbo].[Table1]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

	**Criar Tabela2**

		CREATE TABLE [dbo].[Table2]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

2. Na **Tabela1**, adicione alguns dados de exemplo. Deixe a **Tabela2** vazia. Importaremos dados da **Tabela1** para o Repositório Data Lake. Em seguida, exportaremos dados do Repositório Data Lake Store para a **Tabela2**. Execute o trecho de código a seguir.

		 
		INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## Usar o Sqoop de um cluster HDInsight com acesso ao Repositório Azure Data Lake

Um cluster HDInsight já tem os pacotes Sqoop disponíveis. Se você tiver configurado o cluster HDInsight para usar o Repositório Data Lake como armazenamento adicional, poderá usar o Sqoop (sem alterações de configuração) para importar/exportar dados entre um banco de dados relacional (neste exemplo, o Banco de Dados SQL do Azure) e uma conta do Repositório Data Lake.

1. Para este tutorial, vamos supor que você tenha criado um cluster Linux para poder usar o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Verifique se você pode acessar a conta do Repositório Data Lake do cluster. Execute o comando a seguir do prompt do SSH:

		
		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

	Isso deve fornecer uma lista de arquivos/pastas na conta de Repositório do Data Lake.

### Importar dados do Banco de Dados SQL do Azure para o Repositório Data Lake

3. Navegue até o diretório onde os pacotes do Sqoop estão disponíveis. Normalmente, será `/usr/hdp/<version>/sqoop/bin`. 

4. Importe os dados da **Tabela1** para a conta do Repositório Data Lake. Use a seguinte sintaxe:

		
		sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

	Observe que o espaço reservado **sql-database-server-name** representa o nome do servidor onde o banco de dados SQL do Azure está em execução. O espaço reservado **sql-database-name** representa o nome do banco de dados real.

	Por exemplo,

		
		sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Verifique se os dados foram transferidos para a conta do Repositório Data Lake. Execute o comando a seguir:

		
		hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

	Você deve ver a saída a seguir.

		
		-rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
		-rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
		-rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
		-rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
		-rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

	Cada arquivo **part-m-*** corresponde a uma linha na tabela de origem, a **Tabela1**. Você pode exibir o conteúdo dos arquivos part-m-* para verificação.


### Exportar dados do Repositório Data Lake para o Banco de Dados SQL do Azure

6. Exporte os dados da conta do Repositório Data Lake para a tabela vazia, a **Tabela2**, no Banco de Dados SQL do Azure. Use a sintaxe a seguir.

		
		sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

	Por exemplo,

		
		sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Verifique se os dados foram carregados na tabela do Banco de Dados SQL. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para se conectar ao Banco de Dados SQL Azure e então execute a consulta a seguir.

		
		SELECT * FROM TABLE2

	Isso deverá ter a saída a seguir.

	 	ID  FName   LName
		------------------
		1	Neal	Kell
		2	Lila	Fulton
		3	Erna	Myers
		4	Annette	Simpson

## Consulte também

- [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0302_2016-->