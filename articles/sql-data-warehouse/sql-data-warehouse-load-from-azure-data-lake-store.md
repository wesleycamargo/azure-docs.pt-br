---
title: 'Tutorial: carregar do Azure Data Lake Store para o SQL Data Warehouse do Azure | Microsoft Docs'
description: Use as tabelas externas do PolyBase para carregar dados do Azure Data Lake Store para o SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 04676db3048cf747e9a20d91a404f29c6cfc6853
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306386"
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Carregar dados do Azure Data Lake Store no SQL Data Warehouse
Use as tabelas externas do PolyBase para carregar dados do Azure Data Lake Store para o SQL Data Warehouse do Azure. Embora você possa executar consultas ad hoc em dados armazenados em ADLS, recomendamos importar os dados para o SQL Data Warehouse para obter o melhor desempenho.

> [!div class="checklist"]
> * Crie os objetos de banco de dados necessários para serem carregados do Azure Data Lake Store.
> * Conecte-se a um diretório do Azure Data Lake Store.
> * Carregue os dados no SQL Data Warehouse.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar este tutorial, baixe e instale a versão mais recente do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio).

Para este tutorial, você precisa do:

* Aplicativo do Azure Active Directory para usar como autenticação de serviço a serviço. Para criar, siga a [Autenticação do Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Você precisa do ID do cliente, a chave e o valor do ponto de extremidade de Token OAuth2.0 do seu aplicativo do Active Directory para conectar-se no Azure Data Lake do SQL Data Warehouse. Os detalhes de como obter esses valores estão no link acima. Para registro do aplicativo do Azure Active Directory, use a ID do aplicativo como a ID do cliente.
> 

* Um SQL Data Warehouse do Azure. Consulte [Criar e consultar um SQL Data Warehouse do Azure](create-data-warehouse-portal.md).

* Um Azure Data Lake Store. Consulte [Introdução ao Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Criar uma credencial
Para acessar o Azure Data Lake Store, você precisa criar uma chave mestra de banco de dados para criptografar seu segredo de credencial, que é usado na próxima etapa. Então, você cria uma Credencial com escopo de banco de dados, que armazena as credenciais de entidade de serviço configuradas no AAD. Para aqueles que já usaram o PolyBase para se conectar ao Azure Storage Blob do Windows, observe que a sintaxe da credencial é diferente.

Para se conectar ao armazenamento do Azure Data Lake Store, você deve **primeiro** criar um aplicativo do Azure Active Directory, criar uma tecla de acesso e conceder acesso de aplicativo para o recurso do Azure Data Lake. Para obter instruções, consulte [Autenticação para o Azure Data Lake Store usando o Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Criar a fonte de dados externa
Use este comando [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) para armazenar o local dos dados. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Configurar o formato de dados
Para importar os dados do ADLS, você precisa especificar o Formato de Arquivo Externo. Este objeto define como os arquivos são gravados em ADLS.
Para ver uma lista completa, consulte nossa documentação de T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Criar as tabelas externas
Agora que você especificou o formato do arquivo e da fonte de dados, você está pronto para criar as tabelas externas. As tabelas externas servem para interagir com dados externos. O parâmetro de local pode especificar um arquivo ou diretório. Se ele especificar um diretório, todos os arquivos dentro do diretório serão carregados.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considerações sobre a tabela externa
É fácil criar uma tabela externa, mas existem algumas nuances que precisam ser abordadas.

As Tabelas Externas são fortemente tipadas. Isso significa que cada linha dos dados que estão sendo ingeridos deve atender à definição de esquema da tabela.
Se uma linha não coincidir com a definição de esquema, a linha é rejeitada da carga.

As opções REJECT_TYPE e REJECT_VALUE permitem que você defina quantas linhas ou qual o percentual de dados deve estar presente na tabela final. Durante o carregamento, se o valor rejeitado for atingido, o carregamento falha. A causa mais comum de rejeição de linhas é uma incompatibilidade da definição do esquema. Por exemplo, se uma coluna é especificada incorretamente para o esquema como inteiros, quando os dados no arquivo são de uma cadeia de caracteres, todas as linhas falham ao carregar.

 O armazenamento do Azure Data Lake usa o Controle de Acesso Baseado em Função (RBAC) para controlar o acesso aos dados. Isso significa que a entidade de serviço deve ter permissões de leitura para os diretórios definidos no parâmetro de local e para os filhos do diretório final e dos arquivos. Isso permite ao PolyBase autenticar e carregar os dados. 

## <a name="load-the-data"></a>Carregar os dados
Para carregar dados do Azure Data Lake Store, use a instrução [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse). 

O CTAS cria uma nova tabela e a preenche com os resultados de uma instrução select. CTAS define a nova tabela para ter as mesmas colunas e tipos de dados como os resultados da instrução select. Se você selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e dos tipos de dados na tabela externa.

Neste exemplo, estamos criando uma tabela distribuída de hash chamada DimProduct da nossa tabela externa DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Otimizar a compactação columnstore
Por padrão, o SQL Data Warehouse armazena a tabela como um índice columnstore clusterizado. Após a conclusão do carregamento, algumas das linhas de dados não podem ser compactadas no columnstore.  Há várias razões para isso acontecer. Para obter mais informações, confira [gerenciar índices columnstore](sql-data-warehouse-tables-index.md).

Para otimizar o desempenho da consulta e a compactação columnstore após um carregamento, recrie a tabela para forçar o índice columnstore a compactar todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar estatísticas
É melhor criar estatísticas de coluna única imediatamente após um carregamento. Há algumas opções de estatísticas. Por exemplo, se você criar estatísticas de coluna única em todas as colunas, poderá levar muito tempo para recompilar todas as estatísticas. Se você souber que determinadas colunas não estarão em predicados de consulta, você poderá ignorar a criação de estatísticas nessas colunas.

Se você decidir criar estatísticas com uma coluna em cada coluna de cada tabela, poderá usar o exemplo de código do procedimento armazenado `prc_sqldw_create_stats` no artigo [estatísticas](sql-data-warehouse-tables-statistics.md).

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensões e em cada coluna de junção das tabelas de fatos. Você poderá adicionar estatísticas de coluna única ou múltipla às colunas de tabelas de fatos posteriormente.

## <a name="achievement-unlocked"></a>Missão cumprida!
Você carregou com êxito os dados no SQL Data Warehouse do Azure. Bom trabalho!

## <a name="next-steps"></a>Próximas etapas 
Você criou tabelas externas para definir a estrutura dos dados armazenados no Azure Data Lake Store e depois usou a instrução CREATE TABLE AS SELECT do PolyBase para carregar os dados para seu data warehouse. 

Você fez essas coisas:
> [!div class="checklist"]
> * Foram criados os objetos de banco de dados necessários para carregamento do Azure Data Lake Store.
> * Conexão estabelecida com um diretório do Azure Data Lake Store.
> * Os dados foram carregados para o SQL Data Warehouse do Azure.
> 

O carregamento de dados é a primeira etapa para desenvolver uma solução de data warehouse usando o SQL Data Warehouse. Confira nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
>[Saiba como desenvolver tabelas no SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




