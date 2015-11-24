<properties
	pageTitle="SQL Na Memória, introdução | Microsoft Azure"
	description="As tecnologias Na Memória do SQL melhoram muito o desempenho de cargas de trabalho transacionais e analíticas. Saiba como aproveitar as vantagens dessas tecnologias."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/16/2015"
	ms.author="jodebrui"/>


# Introdução a Na Memória (visualização) no Banco de Dados SQL

As tabelas SQL regulares são armazenadas apenas em um disco rígido. Usando os recursos de Na Memória, é possível aprimorar uma tabela para que ela resida na memória ativa enquanto o servidor estiver em execução.


As tecnologias Na Memória melhoram muito o desempenho de cargas de trabalho transacionais e analíticas.

- Com o OLTP Na Memória (processamento de transações online), você pode alcançar um ganho de até 30 vezes na taxa de transferência de transações, dependendo das especificações da carga de trabalho.
 - Tabelas com otimização de memória.
 - Procedimentos armazenados compilados nativamente.

- Com a Análise Na Memória, você pode obter uma melhoria de até 100 vezes no desempenho da consulta.
 - Índices columnstore.


Para [análise em tempo real](http://msdn.microsoft.com/library/dn817827.aspx), combine essas tecnologias para obter:

- Visão de negócios em tempo real com base em dados operacionais.
- OLTP muito rápido.


#### Disponibilidade


- *GA:* a Análise Na Memória está na Disponibilidade Geral (GA).
- *Visualização:* o OLTP Na Memória e a Análise Operacional em Tempo Real existem em versão de Visualização.
 - Esses dois estão disponíveis apenas para bancos de dados SQL do Azure [Premium](sql-database-service-tiers.md).


#### OLTP e Análise

As duas principais seções neste tópico são:

- R. [OLTP](#install_oltp_manuallink) Na Memória, que envolve leitura e gravação.
- B. [Análise](#install_analytics_manuallink) Na Memória, que envolve leitura.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## R. Instalar o exemplo de OLTP Na Memória.

Você pode criar o banco de dados de exemplo AdventureWorksLT [V12] com alguns cliques no [portal de visualização do Azure](http://portal.azure.com/). Em seguida, as etapas desta seção explicam como é possível enriquecer seu banco de dados AdventureWorksLT com:

- Tabelas Na Memória.
- Um procedimento armazenado compilado nativamente.


#### Etapas de instalação

1. No [portal de visualização do Azure](http://portal.azure.com/), crie um banco de dados Premium em um servidor V12. Defina **Origem** como o banco de dados de exemplo AdventureWorksLT [V12].
 - Para obter instruções detalhadas, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md).

2. Conecte-se ao banco de dados com o [SQL Server Management Studio (SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script Transact-SQL do OLTP Na Memória](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) para a área de transferência.
 - O script T-SQL cria os objetos necessários Na Memória no banco de dados de exemplo AdventureWorksLT criado na etapa 1.

4. Cole o script T-SQL no SSMS e execute o script.
 - As instruções CREATE TABLE da cláusula `MEMORY_OPTIMIZED = ON` são cruciais, como em:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### Erro 40536


Se você receber o erro 40536 quando executar o script T-SQL, execute o seguinte script T-SQL para verificar se o banco de dados oferece suporte a Na Memória:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado **0** significa que não há suporte para Na Memória e 1 significa que há suporte.


#### Sobre os itens criados com otimização de memória

**Tabelas**: o exemplo contém as seguintes tabelas com otimização de memória:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Você pode inspecionar as tabelas com otimização de memória por meio do **Pesquisador de Objetos** do SSMS:

- Clique com o botão direito do mouse em **Tabelas** > **Filtrar** > **Configurações do Filtro** > **Com Otimização de Memória** igual a 1.


Ou você pode consultar as exibições do catálogo, como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Procedimento armazenado compilado nativamente**: SalesLT.usp\_InsertSalesOrder\_inmem pode ser inspecionado por meio do Pesquisador de Objetos ou de consultas da exibições de catálogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## Executar a carga de trabalho OLTP

A única diferença entre os dois *procedimentos armazenados* a seguir é que o primeiro procedimento usa versões com otimização de memória das tabelas, enquanto o segundo procedimento usa as tabelas em disco regulares:

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


Nesta seção, você verá como usar o utilitário **ostress.exe** para executar os dois procedimentos armazenados em níveis estressantes. Você pode comparar quanto tempo as duas execuções demoram para serem concluídas.


Quando você executar ostress.exe, será recomendável passar valores de parâmetro projetados para ambos:

- Execute um grande número de conexões simultâneas usando, talvez, -n100.
- Faça com que cada conexão entre em loop centenas de vezes usando, talvez, -r500.


No entanto, talvez você queira começar com valores muito menores, como -n10 e -r50 para garantir que tudo esteja funcionando.


### Script para ostress.exe


Esta seção exibe o script T-SQL, que está inserido em nossa linha de comando do ostress.exe. O script usa itens que foram criados pelo script T-SQL instalado anteriormente.


O script a seguir insere um pedido de vendas de exemplo com cinco itens de linha nas seguintes *tabelas* com otimização de memória:

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


```
DECLARE
	@i int = 0,
	@od SalesLT.SalesOrderDetailType_inmem,
	@SalesOrderID int,
	@DueDate datetime2 = sysdatetime(),
	@CustomerID int = rand() * 8000,
	@BillToAddressID int = rand() * 10000,
	@ShipToAddressID int = rand() * 10000;
	
INSERT INTO @od
	SELECT OrderQty, ProductID
	FROM Demo.DemoSalesOrderDetailSeed
	WHERE OrderID= cast((rand()*60) as int);
	
EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
	@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


Para criar a versão \_ondisk do T-SQL anterior para ostress.exe, simplesmente substitua ambas as ocorrências da subcadeia de caracteres *\_inmem* por *\_ondisk*. Essas substituições afetam os nomes de tabelas e os procedimentos armazenados.


### Instalar utilitários RML e o ostress


O ideal é você planejar executar o ostress.exe em uma VM do Azure. Você criaria uma [Máquina Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica do Azure onde seu banco de dados AdventureWorksLT reside. Mas você pode executar o ostress.exe em seu laptop.


Na VM ou em qualquer host que você escolher, instale os utilitários RML (Replay Markup Language), que incluem o ostress.exe.

- Consulte a discussão sobre o ostress.exe em [Extensões do AdventureWorks para demonstrar o OLTP Na Memória](http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx).
 - Ou consulte [Banco de dados de exemplo para OLTP Na Memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou consulte [Blog da instalação do ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014, whereas mt465764.aspx is for SQL 2016.
-->



### Executar a carga de trabalho de estresse do \_inmem primeiro


Você pode usar uma janela *Prompt Cmd RML* para executar nossa linha de comando do ostress.exe.

Quando executado a partir do Prompt Cmd RML, o seguinte comando ostress.exe:

- Insere um milhão de pedidos de vendas, com cinco itens de linha cada, nas tabelas com otimização de memória.
- Usa 100 conexões simultâneas (-n100).


```
ostress.exe -n100 -r500 -S<servername>.database.windows.net
	 -U<login> -P<password> -d<database>
	 -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
	 @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
	 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*
	 10000;
	 INSERT INTO @od SELECT OrderQty, ProductID FROM
	 Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int);
	 WHILE (@i < 20) begin;
	 EXECUTE SalesLT.usp_InsertSalesOrder_inmem
	 @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID,
	 @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando do ostress.exe anterior:


1. Redefina o banco de dados ao executar o seguinte comando no SSMS para excluir todos os dados inseridos por todas as execuções anteriores: ```
EXECUTE Demo.usp_DemoReset;
```

2. Copie o texto para a sua área de transferência.

3. Substitua todos os caracteres de fim de linha (\\r\\n) e todas as tabulações (\\t) por espaços.

4. Substitua o <placeholders> para os parâmetros -S -U -P -d pelos valores reais corretos.


#### O resultado é uma duração


Quando o ostress.exe é concluído, ele grava a duração da execução como sua linha final de saída na janela Cmd RML. Por exemplo, uma execução de teste mais curta dura aproximadamente 1,5 minuto:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Redefinir, editar para \_ondisk e executar novamente


Depois de ter o resultado da execução do \_inmem, realize as seguintes etapas para a execução de \_indisk:


1. Redefina o banco de dados ao executar o seguinte comando no SSMS para excluir todos os dados inseridos por pela execução anterior: ```
EXECUTE Demo.usp_DemoReset;
```

2. Edite a linha de comando do ostress.exe para substituir todos *\_inmem* por *\_ondisk*.

3. Execute novamente o ostress.exe e capture o resultado da duração.

4. Novamente, redefina o banco de dados para fazer uma limpeza responsável.
 - Uma única execução de teste inserindo um milhão de resultados de pedidos de vendas em 500 MB ou mais de dados nas tabelas.


#### Resultados esperados para a comparação

Os testes mostraram um aprimoramento de desempenho em torno de **nove vezes** melhor em comparação com as tabelas baseadas em disco para esta carga de trabalho, com o ostress sendo executado em uma VM na mesma região do Azure que o banco de dados.

A melhoria de desempenho pode ser maior quando a conversão em procedimentos armazenados compilados nativamente é adicionada.


## B. Instalar o exemplo de Análise Na Memória


Nesta seção, você vai comparar os resultados de E/S e de Estatísticas ao usar um índice columnstore versus um índice regular.


Os índices columnstore são logicamente iguais aos índices regulares, mas fisicamente são diferentes. Um índice columnstore organiza os dados de forma exótica para compactá-los bastante. Isso oferece grandes aprimoramentos de desempenho.


Para fazer uma análise em tempo real em uma carga de trabalho OLTP, quase sempre será melhor usar um índice columnstore NÃO clusterizado. Para obter detalhes, consulte [Índices columnstore descritos](http://msdn.microsoft.com/library/gg492088.aspx).



### Preparar o teste de análise de columnstore


1. Use o portal do Azure para criar um novo banco de dados AdventureWorksLT desde o exemplo.
 - Use esse nome exato.
 - Escolha qualquer camada de serviço Premium.

2. Copie o [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) para sua área de transferência.
 - O script T-SQL cria os objetos necessários Na Memória no banco de dados de exemplo AdventureWorksLT criado na etapa 1.
 - O script cria a tabela Dimension e duas tabelas de fatos. As tabelas de fatos são preenchidas com 3,5 milhões de linhas cada.
 - O script pode levar 15 minutos para ser concluído.

3. Cole o script T-SQL no SSMS e execute o script.
 - A palavra-chave **COLUMNSTORE** é crucial em uma instrução **CREATE INDEX**, como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Defina o AdventureWorksLT para um nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`


#### Tabelas e índices columnstore cruciais


- dbo.FactResellerSalesXL\_CCI é uma tabela com um índice **columnstore** clusterizado, que tem compactação avançada no nível de *dados*.

- dbo.FactResellerSalesXL\_PageCompressed é uma tabela com um índice clusterizado regular equivalente, compactado somente no nível de *página*.


#### Consultas cruciais para comparar o índice columnstore


[Estes](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) são os diversos tipos de consulta T-SQL que você pode executar para ver as melhorias de desempenho. Na Etapa 2 no script T-SQL, há um par de consultas que são de interesse direto. As duas consultas diferem apenas em uma única linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Há um índice columnstore clusterizado na tabela FactResellerSalesXL**\_CCI**.

O seguinte trecho de script T-SQL imprime estatísticas de E/S e de TIME para a consulta de cada tabela.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```


## Considerações de visualização sobre Na Memória


Os recursos de Na Memória no Banco de Dados SQL do Azure se tornaram [ativos para visualização em 28 de outubro de 2015](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Durante a fase de Visualização antes da Disponibilidade Geral (GA), o OLTP Na Memória só tem suporte para:

- Bancos de dados que estejam na camada de serviço *Premium*.

- Bancos de dados que tenham sido criados depois que os recursos de Na Memória se tornaram ativos.
 - Um novo banco de dados restaurado de um backup feito antes da data de ativação de Na Memória não poderá dar suporte a Na Memória.
 - Suponha que você tenha feito backup de um banco de dados que ofereça suporte a Na Memória. Então, você restaura o backup em um banco de dados Premium antigo. O banco de dados antigo agora dá suporte a Na Memória.


Em caso de dúvida, você também poderá executar o seguinte T-SQL SELECT para averiguar se seu banco de dados dá suporte a OLTP Na Memória. Um resultado **1** significa que o banco de dados dá suporte a Na Memória:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Se a consulta retornar **1**, o OLTP Na Memória tem suporte neste banco de dados, bem como em qualquer cópia do banco de dados e restauração de banco de dados criada com base nesse banco de dados.


#### Objetos permitidos apenas no Premium


Se um banco de dados contiver qualquer um dos seguintes tipos de objetos ou de tipos OLTP Na Memória, o downgrade da camada de serviço do banco de dados para Basic ou Standard não terá suporte. Para fazer o downgrade do banco de dados, primeiro descarte estes objetos:

- Tabelas com otimização de memória
- Tipos de tabela com otimização de memória
- Módulos compilados nativamente


#### Outros relacionamentos


- O uso de recursos OLTP Na Memória com bancos de dados em pools elásticos não tem suporte durante a Visualização, mas poderá ter suporte no futuro:

- Usando OLTP Na Memória com o SQL Data Warehouse não tem suporte.
 - O recurso de índice columnstore da Análise Na Memória tem suporte no SQL Data Warehouse.

- O Armazenamento de Consultas não captura consultas dentro de módulos compilados nativamente durante a Visualização, mas poderá fazer isso no futuro.

- Alguns recursos Transact-SQL não têm suporte com o OLTP Na Memória. Isso se aplica ao Microsoft SQL Server e ao Banco de Dados SQL do Azure. Para obter mais informações, consulte:
 - [Suporte a Transact-SQL para OLTP em memória](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Construções Transact-SQL sem suporte do OLTP Na Memória](http://msdn.microsoft.com/library/dn246937.aspx)


## Etapas adicionais


- Experimente [Usar o OLTP Na Memória em um aplicativo SQL do Azure existente.](sql-database-in-memory-oltp-migration.md)


## Recursos adicionais

#### Informações mais detalhadas

- [Saiba mais sobre o OLTP Na Memória, que se aplica ao Microsoft SQL Server e ao Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre a Análise Operacional em Tempo Real no MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- [White paper sobre padrões comuns de carga de trabalho e considerações sobre migração](http://msdn.microsoft.com/library/dn673538.aspx), que descreve os padrões de carga de trabalho onde o OLTP Na Memória geralmente fornece ganhos significativos de desempenho.

#### Design do aplicativo

- [OLTP Na Memória (Otimização Na Memória)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Usar o OLTP Na Memória em um Aplicativo do SQL Azure existente.](sql-database-in-memory-oltp-migration.md)

#### Ferramentas

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), para a versão mensal mais recente.

- [Descrição dos utilitários Replay Markup Language (RML) para o SQL Server](http://support.microsoft.com/pt-BR/kb/944837)

- [Monitorar o Armazenamento Na Memória](sql-database-in-memory-oltp-monitoring.md) para o OLTP Na Memória.

<!---HONumber=Nov15_HO4-->