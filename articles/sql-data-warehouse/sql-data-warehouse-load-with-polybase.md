<properties
   pageTitle="Tutorial do PolyBase no SQL Data Warehouse | Microsoft Azure"
   description="Saiba o que é o PolyBase e como usá-lo em cenários de data warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# Carregar dados com o PolyBase
A tecnologia PolyBase permite consultar e unir dados de várias fontes tudo usando os comandos Transact-SQL.

Usando o PolyBase, você pode consultar dados armazenados no armazenamento de blob do Azure e carregá-lo nos banco de dados do SQL Data Warehouse seguindo estas etapas:

- Criar a chave mestra e as credenciais do banco de dados.
- Criar objetos do PolyBase: fonte de dados externa, formato de arquivo externo e tabela externa. 
- Consultar dados armazenados no armazenamento de blob do Azure.
- Carregar dados do armazenamento de blob do Azure no SQL Data Warehouse.


## Pré-requisitos
Para acompanhar este tutorial, você precisará:

- Conta de Armazenamento do Azure
- Seus dados armazenados no armazenamento de blob do Azure como arquivos de texto delimitados

Primeiramente, você criará os objetos que o PolyBase exige para se conectar e consultar dados no armazenamento de blob do Azure.

## Criar chave mestra do banco de dados
Conecte-se ao banco de dados em seu servidor para criar uma chave mestre de banco de dados. Essa chave é usada para criptografar sua credencial na próxima etapa.

```
-- Creating master key
CREATE MASTER KEY;
```

Tópico de referência: [CREATE MASTER KEY (Transact-SQL)][].

## Criar uma credencial com escopo de banco de dados
Para acessar o armazenamento de blob do Azure, você precisa criar uma credencial com escopo de banco de dados que armazene informações de autenticação da sua conta de armazenamento do Azure. Conecte-se ao banco de dados do seu data warehouse e crie uma credencial com escopo de banco de dados para cada conta de armazenamento do Azure que deseja acessar. Especifique um nome de identidade e a chave da conta de armazenamento do Azure como o Segredo. O nome da identidade não afeta a autenticação no Armazenamento do Azure.

Para ver se já existe uma credencial no escopo do banco de dados, use sys.database\_credentials, e não sys.credentials, que mostra apenas as credenciais do servidor.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

Tópico de referência: [CREATE CREDENTIAL (Transact-SQL)][].

Quando você alterna suas chaves de conta de armazenamento do Azure, é preciso descartar a credencial e recriá-la usando a nova chave como o Segredo.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

Tópico de referência: [DROP CREDENTIAL (Transact-SQL)][].


## Criar uma fonte de dados externa
A fonte de dados externa é um objeto de banco de dados que armazenam o local dos dados de armazenamento de blob do Azure e suas informações de acesso. É necessário definir uma fonte de dados externa para cada contêiner de Armazenamento do Azure que você deseja acessar.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

Tópico de referência: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## Criar um formato de arquivo externo
O formato de arquivo externo é um objeto de banco de dados que especifica o formato dos dados externos. Neste exemplo, descompactamos dados em um arquivo de texto e os campos são separados pelo caractere de barra vertical (‘|’).

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

O PolyBase pode trabalhar com dados compactados e descompactados em texto delimitado, formatos Hive RCFILE e HIVE ORC.

Tópico de referência: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## Criar uma tabela externa

A definição de tabela externa é semelhante a uma definição de tabela relacional. As principais diferenças são o local e o formato dos dados. A definição de tabela externa é armazenada no banco de dados do SQL Data Warehouse. Os dados são armazenados no local especificado pela fonte de dados.

A opção LOCATION especifica o caminho para os dados a partir da raiz da fonte de dados. Neste exemplo, os dados estão localizados em 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]Observe que, neste momento, não será possível criar estatísticas sobre uma tabela externa.

Todos os arquivos para a mesma tabela precisam estar na mesma pasta lógica no BLOB do Azure. Como prática recomendada, divida os dados do Armazenamento do Azure em arquivos que não ultrapassem 1 GB, quando possível, para processamento paralelo com o SQL Data Warehouse.

Tópico de referência: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Os objetos que você acabou de criar são armazenados no banco de dados do SQL Data Warehouse. Você pode exibi-los no Pesquisador de Objetos do SSDT (SQL Server Data Tools).


## Consultar dados de armazenamento de blob do Azure
As consultas em tabelas externas simplesmente usam o nome da tabela como se ele fosse uma tabela relacional.

Esta é uma consulta ad hoc que une dados de seguros de clientes no SQL Data Warehouse com dados de sensor de automóveis armazenados no blob de armazenamento do Azure. O resultado mostra os motoristas que dirigem mais rapidamente que outros.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Carregar dados do armazenamento de blob do Azure
Este exemplo carrega dados do armazenamento de blob do Azure no banco de dados do SQL Data Warehouse.

Armazenar dados diretamente elimina o tempo de transferência de dados para consultas. Armazenar dados com um índice columnstore melhora o desempenho de consultas de análise em até 10 vezes.

Este exemplo usa a instrução CREATE TABLE AS SELECT para carregar dados. A nova tabela herda as colunas nomeadas na consulta. Ela herda os tipos de dados dessas colunas da definição de tabela externa.

CREATE TABLE AS SELECT é uma instrução de alto desempenho do Transact-SQL que substitui INSERT...SELECT. Ela foi originalmente desenvolvida para o mecanismo MPP (processamento massivamente paralelo) no Analytics Platform System e agora está no SQL Data Warehouse.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

Consulte [CREATE TABLE AS SELECT (Transact-SQL)][].


## Limitações
O carregamento de dados com o PolyBase oferece suporte apenas ao estilo de codificação UTF-8. Para outros estilos de codificação, por exemplo, UTF-16, considere o uso do utilitário bcp, SSIS ou Azure Data Factory para carregar dados no banco de dados do SQL Data Warehouse.

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/ms189450.aspx

<!---HONumber=July15_HO5-->