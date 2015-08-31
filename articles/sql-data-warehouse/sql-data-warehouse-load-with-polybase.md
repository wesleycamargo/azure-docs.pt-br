<properties
   pageTitle="Tutorial do PolyBase no SQL Data Warehouse | Microsoft Azure"
   description="Saiba o que é o PolyBase e como usá-lo em cenários de data warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

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
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = 'myazurestoragekey=='
;
```

Tópico de referência: [CREATE CREDENTIAL (Transact-SQL)][].

Para remover uma credencial no escopo do banco de dados, simplesmente use a seguinte sintaxe:

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

Tópico de referência: [DROP CREDENTIAL (Transact-SQL)][].

## Criar uma fonte de dados externa
A fonte de dados externa é um objeto de banco de dados que armazenam o local dos dados de armazenamento de blob do Azure e suas informações de acesso. É necessário definir uma fonte de dados externa para cada contêiner de Armazenamento do Azure que você deseja acessar.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path'
,   CREDENTIAL = ASBSecret
)
;
```

Tópico de referência: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

Para remover a fonte de dados externa, a sintaxe é a seguinte:

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

Tópico de referência: [DROP EXTERNAL DATA SOURCE (Transact-SQL)][].

## Criar um formato de arquivo externo
O formato de arquivo externo é um objeto de banco de dados que especifica o formato dos dados externos. Neste exemplo, descompactamos dados em um arquivo de texto e os campos são separados pelo caractere de barra vertical (‘|’).

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

O PolyBase pode trabalhar com dados compactados e descompactados em texto delimitado, formatos Hive RCFILE e HIVE ORC.

Tópico de referência: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

Para remover um formato de arquivo externo, a sintaxe é a seguinte:

```
-- Dropping external file format...
DROP EXTERNAL FILE FORMAT text_file_format
;
```
Tópico de referência: [DROP EXTERNAL FILE FORMAT (Transact-SQL)][].

## Criar uma tabela externa

A definição de tabela externa é semelhante a uma definição de tabela relacional. As principais diferenças são o local e o formato dos dados. A definição de tabela externa é armazenada no banco de dados do SQL Data Warehouse. Os dados são armazenados no local especificado pela fonte de dados.

A opção LOCATION especifica o caminho para os dados a partir da raiz da fonte de dados. Neste exemplo, os dados estão localizados em 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'. Todos os arquivos para a mesma tabela precisam estar na mesma pasta lógica no BLOB do Azure.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]Observe que, neste momento, não será possível criar estatísticas sobre uma tabela externa.

Tópico de referência: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Os objetos que você acabou de criar são armazenados no banco de dados do SQL Data Warehouse. Você pode exibi-los no Pesquisador de Objetos do SSDT (SQL Server Data Tools).

Para remover uma tabela externa, use a sintaxe a seguir:

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]Ao soltar uma tabela externa, você deve usar `DROP EXTERNAL TABLE`, e **não pode** usar `DROP TABLE`.

Tópico de referência: [DROP EXTERNAL TABLE (Transact-SQL)][].

Também vale a pena observar que as tabelas externas estão visíveis no `sys.tables` e, mais especificamente, nas exibições do catálogo `sys.external_tables`.

## Rotação de chaves de armazenamento

Às vezes você desejará alterar a chave de acesso para o armazenamento de blob por motivos de segurança.

A maneira mais elegante de realizar essa tarefa é seguir um processo conhecido como "rotação das chaves". Você talvez tenha observado que há duas chaves de armazenamento para a conta de armazenamento de blob. Isso é para que você possa fazer a transição

A rotação das suas chaves da conta de Armazenamento do Azure é um processo simples de três etapas

1. Criar uma segunda credencial no escopo do banco de dados com base na chave de acesso de armazenamento secundário
2. Criar a segunda fonte de dados externa com base nessa nova credencial
3. Remover e criar as tabelas externas apontando para a nova fonte de dados externa

Depois de migrar todas as tabelas externas para a nova fonte de dados externa, você pode executar as tarefas de limpeza:
 
1. Remover a primeira fonte de dados externa
2. Remover a primeira credencial no escopo do banco de dados na chave de acesso de armazenamento primário
3. Fazer logon no Azure e regenerar a chave de acesso primária pronta para a próxima vez

## Consultar dados de armazenamento de blob do Azure
As consultas em tabelas externas simplesmente usam o nome da tabela como se ele fosse uma tabela relacional.

Esta é uma consulta ad hoc que une dados de seguros de clientes no SQL Data Warehouse com dados de sensor de automóveis armazenados no blob de armazenamento do Azure. O resultado mostra os motoristas que dirigem mais rapidamente que outros.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
      [Insured_Customers].[FirstName]
,     [Insured_Customers].[LastName]
,     [Insured_Customers].[YearlyIncome]
,     [CarSensor_Data].[Speed]
FROM  [dbo].[Insured_Customers] 
JOIN  [ext].[CarSensor_Data]         ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] DESC
;
```

## Carregar dados do armazenamento de blob do Azure
Este exemplo carrega dados do armazenamento de blob do Azure no banco de dados do SQL Data Warehouse.

Armazenar dados diretamente elimina o tempo de transferência de dados para consultas. Armazenar dados com um índice columnstore melhora o desempenho de consultas de análise em até 10 vezes.

Este exemplo usa a instrução CREATE TABLE AS SELECT para carregar dados. A nova tabela herda as colunas nomeadas na consulta. Ela herda os tipos de dados dessas colunas da definição de tabela externa.

CREATE TABLE AS SELECT é uma instrução de alto desempenho do Transact-SQL que substitui INSERT...SELECT. Ela foi originalmente desenvolvida para o mecanismo MPP (processamento massivamente paralelo) no Analytics Platform System e agora está no SQL Data Warehouse.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

Consulte [CREATE TABLE AS SELECT (Transact-SQL)][].


## Solução alternativa para o requisito de PolyBase UTF-8
No momento, PolyBase dá suporte a carregamento de arquivos de dados codificados em UTF-8. Uma vez que UTF-8 usa a mesma codificação de caractere que ASCII, PolyBase também terá suporte ao carregamento de dados codificado em ASCII. No entanto, PolyBase não tem suporte para outra codificação de caractere, como UTF-16/ Unicode ou caracteres ASCII estendidos. ASCII estendido inclui caracteres com acentos, como o trema, que é comum em alemão.

Para contornar esse requisito, a melhor resposta é reescrever para codificação UTF-8.

Há várias maneiras de fazer isso. A seguir estão as duas abordagens usando o PowerShell:

### Exemplo simples para arquivos pequenos

A seguir está um script simples do PowerShell de uma linha que cria o arquivo.
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

No entanto, embora essa seja uma maneira simples de codificar os dados, de forma alguma é a mais eficiente. O exemplo de fluxo de E/S a seguir é muito mais rápido e atinge o mesmo resultado.

### Exemplo de Streaming de E/S para arquivos maiores

O exemplo de código a seguir é mais complexo, mas, por transmitir as linhas de dados da origem para o destino, é muito mais eficiente. Use essa abordagem para arquivos maiores.

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

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

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/pt-br/library/ms189450.aspx

<!---HONumber=August15_HO8-->