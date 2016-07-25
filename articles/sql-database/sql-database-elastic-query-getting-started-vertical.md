<properties
	pageTitle="Introdução às consultas entre bancos de dados (particionamento vertical) | Microsoft Azure"	
	description="como usar a consulta de banco de dados elástico com bancos de dados particionados verticalmente"
	services="sql-database"
	documentationCenter=""  
	manager="jhubbard"
	authors="torsteng"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="torsteng" />

# Introdução às consultas entre bancos de dados (particionamento vertical) (preview)

A consulta de banco de dados elástico (visualização) para o Banco de Dados SQL do Azure permite executar consultas T-SQL que abrangem vários bancos de dados usando um único ponto de conexão. Este tópico se aplica a [bancos de dados particionados verticalmente](sql-database-elastic-query-vertical-partitioning.md).

Quando tiver concluído, você saberá como configurar e usar um Banco de Dados SQL do Azure para executar consultas que abrangem vários bancos de dados relacionados.

Para saber mais sobre o recurso de consulta de banco de dados elástico, consulte a [visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure](sql-database-elastic-query-overview.md).

## Criar os bancos de dados de exemplo

Para começar, precisamos criar dois bancos de dados, **Customers** e **Orders**, nos mesmos servidores lógicos ou em servidores diferentes.

Execute as consultas a seguir no banco de dados **Orders** para criar a tabela **OrderInformation** e inserir os dados de exemplo.

	CREATE TABLE [dbo].[OrderInformation]( 
		[OrderID] [int] NOT NULL, 
		[CustomerID] [int] NOT NULL 
		) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Agora, execute a consulta a seguir no banco de dados **Customers** para criar a tabela **CustomerInformation** e inserir os dados de exemplo.

	CREATE TABLE [dbo].[CustomerInformation]( 
		[CustomerID] [int] NOT NULL, 
		[CustomerName] [varchar](50) NULL, 
		[Company] [varchar](50) NULL 
		CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
	) 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## Criar objetos de banco de dados
### Chave mestra e credenciais do escopo do banco de dados

1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Conecte-se ao banco de dados Orders e execute os seguintes comandos T-SQL:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
		WITH IDENTITY = '<username>', 
		SECRET = '<password>';  

	O "nome de usuário" e a "senha" devem ser o nome de usuário e a senha usados para fazer logon no banco de dados Customers. Atualmente não há suporte para a autenticação usando o Azure Active Directory com consultas elásticas.

### Fontes de dados externas
Para criar uma fonte de dados externa, execute o seguinte comando no banco de dados Orders:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
		(TYPE = RDBMS, 
		LOCATION = '<server_name>.database.windows.net', 
		DATABASE_NAME = 'Customers', 
		CREDENTIAL = ElasticDBQueryCred, 
	) ;

### Tabelas externas
Crie uma tabela externa ao banco de dados Orders, que corresponde à definição da tabela CustomerInformation:

	CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
	( [CustomerID] [int] NOT NULL, 
	  [CustomerName] [varchar](50) NOT NULL, 
	  [Company] [varchar](50) NOT NULL) 
	WITH 
	( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## Executar uma consulta T-SQL no banco de dados elástico de exemplo

Depois de definir sua fonte de dados e suas tabelas externas, agora você poderá usar o T-SQL para consultar suas tabelas externas. Execute esta consulta no banco de dados Orders:

	SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
	FROM OrderInformation 
	INNER JOIN CustomerInformation 
	ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## Custo

Atualmente, o recurso de consulta de banco de dados elástico está incluído no custo de seu Banco de Dados SQL do Azure.

Para saber mais sobre preços, consulte [Preços do Banco de Dados SQL](/pricing/details/sql-database).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->

<!---HONumber=AcomDC_0713_2016-->