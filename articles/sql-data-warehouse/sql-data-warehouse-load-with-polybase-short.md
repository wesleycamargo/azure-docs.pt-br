<properties
   pageTitle="Tutorial Carregar dados com o PolyBase | Microsoft Azure"
   description="Saiba como usar o PolyBase para carregar dados no SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="sahajs;barbkess"/>


# Carregar dados com o PolyBase
Este tutorial mostrará como carregar dados em seu SQL Data Warehouse do Azure usando o PolyBase.


## Pré-requisitos
Para acompanhar este tutorial, você precisará de

- Um banco de dados do SQL Data Warehouse
- Conta de armazenamento do Azure


## Etapa 1: Criar um arquivo de dados de origem
Abra o Bloco de Notas e copie as linhas de dados a seguir em um novo arquivo. Salve-o no diretório temporário local, C:\\Temp\\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```


## Etapa 2: Migrar dados para o Armazenamento de Blob do Azure

- Baixe a [versão mais recente do AzCopy][].
- Abra uma janela de comando e navegue até o diretório de instalação do AzCopy no computador, onde o AzCopy.exe está localizado. Por padrão, o diretório de instalação é %ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\ em um computador do Windows de 64 bits.
- Execute o comando a seguir para carregar o arquivo. Especifique sua Chave de Conta de Armazenamento do Azure para /DestKey.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

Para saber mais sobre o AzCopy, consulte [Introdução ao utilitário de linha de comando AzCopy][].


## Etapa 3: Criar tabelas externas

Em seguida, será necessário criar tabelas externas no SQL Data Warehouse para consultar os dados no armazenamento de blob do Azure. Para criar uma tabela externa, use as seguintes etapas:

- [Criar Chave Mestra][]\: para criptografar o segredo da credencial com escopo de banco de dados.
- [Criar Credencial com Escopo no Banco de Dados]\: para especificar as informações de autenticação para sua conta de armazenamento do Azure.
- [Criar Fonte de Dados Externa]\: para especificar o local de seu armazenamento de blob do Azure.
- [Criar Formato de Arquivo Externo]\: para especificar o layout dos dados.
- [Criar Tabela Externa]\: para fazer referência aos dados do Armazenamento do Azure.



```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## Etapa 4: Carregar dados no SQL Data Warehouse

- Para carregar os dados em uma nova tabela, execute a instrução CREATE TABLE AS SELECT. A nova tabela herda as colunas nomeadas na consulta. Ela herda os tipos de dados dessas colunas da definição de tabela externa. 
- Para carregar os dados em uma tabela existente, use a instrução INSERÇÃO...SELECT.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
		DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```
Consulte [CREATE TABLE AS SELECT (Transact-SQL)][].


Para saber mais sobre o PolyBase, consulte [Tutorial sobre o PolyBase no SQL Data Warehouse][].


<!--Article references-->
[Tutorial sobre o PolyBase no SQL Data Warehouse]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[versão mais recente do AzCopy]: http://aka.ms/downloadazcopy
[Introdução ao utilitário de linha de comando AzCopy]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Criar Fonte de Dados Externa]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Criar Formato de Arquivo Externo]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Criar Tabela Externa]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Criar Chave Mestra]: https://msdn.microsoft.com/pt-BR/library/ms174382.aspx
[Criar Credencial com Escopo no Banco de Dados]: https://msdn.microsoft.com/pt-BR/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx

<!---HONumber=Oct15_HO4-->