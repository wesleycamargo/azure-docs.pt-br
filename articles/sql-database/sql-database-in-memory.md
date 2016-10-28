<properties
	pageTitle="SQL Na Memória, introdução | Microsoft Azure"
	description="As tecnologias Na Memória do SQL melhoram muito o desempenho de cargas de trabalho transacionais e analíticas. Saiba como aproveitar as vantagens dessas tecnologias."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="jodebrui"/>


# Introdução a Na Memória (visualização) no Banco de Dados SQL

Os recursos Na Memória melhoram muito o desempenho de cargas de trabalho transacionais e analíticas nas situações certas.

Este tópico enfatiza duas demonstrações, uma para o OLTP in-memory e outra para a Análise in-memory. Cada demonstração vem completa com as etapas e o código de que você precisa para executar a demonstração. Você pode:

- Usar o código para testar as variações e ver as diferenças nos resultados de desempenho ou
- Ler o código para entender o cenário e para saber como criar e utilizar os objetos Na Memória.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [Início Rápido 1: tecnologias OLTP in-memory para um desempenho mais rápido do T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) – é outro artigo para ajudá-lo a se familiarizar.

#### OLTP Na Memória

Os recursos do [OLTP](#install_oltp_manuallink) Na Memória (processamento de transações online) são:

- Tabelas com otimização de memória.
- Procedimentos armazenados compilados nativamente.


Uma tabela com otimização de memória tem uma representação de si mesmo em memória ativa, além da representação padrão em um disco rígido. As transações de negócios na tabela são executadas mais rapidamente porque elas interagem diretamente apenas com a representação na memória ativa.

Com o OLTP in-memory, você pode alcançar um ganho de até 30 vezes na taxa de transferência de transações, dependendo das especificações da carga de trabalho.


Os procedimentos armazenados compilados nativamente exigem menos instruções de computador durante o tempo de execução do que procedimentos armazenados interpretados tradicionais. Vimos o resultado da compilação nativa em durações que são um 1/100º da duração interpretada.


#### Análise Na Memória 

O recurso de [Análise](#install_analytics_manuallink) Na Memória é:

Índices Columnstore melhoram o desempenho de consultas de relatórios e de análise.


#### Análise em Tempo Real

Para a [Análise em Tempo Real](http://msdn.microsoft.com/library/dn817827.aspx), você combina o OLTP e a Análise Na Memória para obter:

- Visão de negócios em tempo real com base em dados operacionais.


#### Disponibilidade


GA, Disponibilidade Geral:

- [Índices columnstore](http://msdn.microsoft.com/library/dn817827.aspx) que estão *em disco*.


Visualização:

- OLTP Na Memória
- Análise Operacional em Tempo Real


As considerações sobre os recursos Na Memória em versão de preview serão descritas [mais adiante neste tópico](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Esses recursos em Visualização estão disponíveis apenas para bancos de dados SQL do Azure [*Premium*](sql-database-service-tiers.md) e não para bancos de dados na camada de serviço Standard ou Basic.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## R. Instalar o exemplo de OLTP Na Memória.

Você pode criar o banco de dados de exemplo AdventureWorksLT [V12] com alguns cliques no [Portal do Azure](https://portal.azure.com/). Em seguida, as etapas desta seção explicam como é possível enriquecer seu banco de dados AdventureWorksLT com:

- Tabelas Na Memória.
- Um procedimento armazenado compilado nativamente.


#### Etapas de instalação

1. No [Portal do Azure](https://portal.azure.com/), crie um banco de dados Premium em um servidor V12. Defina a **Origem** como o banco de dados de exemplo AdventureWorksLT [V12].
 - Para obter instruções detalhadas, consulte [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

2. Conecte-se ao banco de dados com o SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script Transact-SQL do OLTP Na Memória](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) para a área de transferência.
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


Um resultado **0** significa que não há suporte para Na Memória e 1 significa que há suporte. Para diagnosticar o problema:

- Verifique se o banco de dados foi criado depois que os recursos de OLTP Na Memória tenham se tornado ativos para a Visualização.
- Verifique se o banco de dados está na camada de serviço Premium.


#### Sobre os itens criados com otimização de memória

**Tabelas**: o exemplo contém as seguintes tabelas com otimização de memória:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Você pode inspecionar as tabelas com otimização de memória por meio do **Pesquisador de Objetos** no SSMS:

- Clique com o botão direito do mouse em **Tabelas** > **Filtrar** > **Configurações do Filtro** > **Com Otimização de Memória** igual a 1.


Ou você pode consultar as exibições do catálogo, como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Procedimento armazenado compilado nativamente**: SalesLT.usp\_InsertSalesOrder\_inmem pode ser inspecionado por meio de uma consulta de exibições de catálogo:


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

- Execute um grande número de conexões simultâneas usando -n100.
- Faça com que cada conexão entre em loop centenas de vezes usando -r500.


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
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
	SET @i = @i + 1;
end
```


Para criar a versão \_ondisk do T-SQL anterior para ostress.exe, simplesmente substitua ambas as ocorrências da subcadeia de caracteres *\_inmem* por *\_ondisk*. Essas substituições afetam os nomes de tabelas e os procedimentos armazenados.


### Instalar utilitários RML e o ostress


O ideal é você planejar executar o ostress.exe em uma VM do Azure. Você criaria uma [Máquina Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica do Azure onde seu banco de dados AdventureWorksLT reside. Mas você pode executar o ostress.exe em seu laptop.


Na VM, ou em qualquer host que você escolher, instale os utilitários RML (Replay Markup Language), que incluem o ostress.exe.

- Veja a discussão sobre ostress.exe no [Banco de dados de exemplo para OLTP Na Memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou veja [Banco de dados de exemplo para OLTP Na Memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou veja [Blog da instalação do ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### Executar a carga de trabalho de estresse do \_inmem primeiro


Você pode usar uma janela *Prompt Cmd RML* para executar nossa linha de comando do ostress.exe. Os parâmetros de linha de comando direcionam o ostress para:

- Execute 100 conexões simultaneamente (-n100).
- Faça cada conexão executar o script T-SQL 50 vezes (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando do ostress.exe anterior:


1. Redefina o conteúdo de dados do banco de dados executando o seguinte comando no SSMS para excluir todos os dados inseridos por todas as execuções anteriores:
```
EXECUTE Demo.usp_DemoReset;
```

2. Copie o texto da linha de comando anterior do ostress.exe para a área de transferência.

3. Substitua o `<placeholders>` para os parâmetros -S -U -P -d pelos valores reais corretos.

4. Execute a linha de comando editada em uma janela Cmd RML.


#### O resultado é uma duração


Quando o ostress.exe é concluído, ele grava a duração da execução como sua linha final de saída na janela Cmd RML. Por exemplo, uma execução de teste mais curta dura aproximadamente 1,5 minuto:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Redefinir, editar para \_ondisk e executar novamente


Depois de ter o resultado da execução do \_inmem, realize as seguintes etapas para a execução de \_ondisk:


1. Redefina o banco de dados executando o seguinte comando no SSMS para excluir todos os dados inseridos pela execução anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Edite a linha de comando do ostress.exe para substituir todos os *\_inmem* por *\_ondisk*.

3. Execute novamente o ostress.exe pela segunda vez e capture o resultado da duração.

4. Redefina novamente o banco de dados para exclusão responsável do que puder ser uma grande quantidade de dados de teste.


#### Resultados esperados para a comparação

Os testes in-memory mostraram uma melhoria de desempenho de **nove vezes** para essa carga de trabalho simplista, com o ostress sendo executado em uma VM do Azure na mesma região do Azure que o banco de dados.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## B. Instalar o exemplo de Análise Na Memória


Nesta seção, você vai comparar os resultados de E/S e de Estatísticas ao usar um índice columnstore versus um índice b-tree tradicional.


Para fazer uma análise em tempo real em uma carga de trabalho OLTP, quase sempre será melhor usar um índice columnstore NÃO clusterizado. Para obter detalhes, veja [Índices columnstore descritos](http://msdn.microsoft.com/library/gg492088.aspx).



### Preparar o teste de análise de columnstore


1. Use o portal do Azure para criar um novo banco de dados AdventureWorksLT desde o exemplo.
 - Use esse nome exato.
 - Escolha qualquer camada de serviço Premium.

2. Copie o [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) para sua área de transferência.
 - O script T-SQL cria os objetos necessários Na Memória no banco de dados de exemplo AdventureWorksLT criado na etapa 1.
 - O script cria a tabela Dimension e duas tabelas de fatos. As tabelas de fatos são preenchidas com 3,5 milhões de linhas cada.
 - O script pode levar 15 minutos para ser concluído.

3. Cole o script T-SQL no SSMS e execute o script.
 - A palavra-chave **COLUMNSTORE** é crucial em uma instrução **CREATE INDEX**, como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Defina o AdventureWorksLT com um nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - O nível 130 não está diretamente relacionado aos recursos Na Memória. Mas o nível 130 geralmente oferece desempenho de consulta mais rápido que o 120.


#### Tabelas e índices columnstore cruciais


- dbo.FactResellerSalesXL\_CCI é uma tabela com um índice **columnstore** clusterizado, que tem compactação avançada no nível de *dados*.

- dbo.FactResellerSalesXL\_PageCompressed é uma tabela com um índice clusterizado regular equivalente, compactado somente no nível de *página*.


#### Consultas cruciais para comparar o índice columnstore


[Estes](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) são os diversos tipos de consulta T-SQL que você pode executar para ver as melhorias de desempenho. Na Etapa 2 no script T-SQL, há um par de consultas que são de interesse direto. As duas consultas diferem apenas em uma única linha:


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



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## Considerações de visualização sobre OLTP Na Memória


Os recursos do OLTP Na Memória no Banco de Dados SQL do Azure se tornaram [ativos para visualização em 28 de outubro de 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Na preview atual, só há suporte do OLTP in-memory para:

- Os bancos de dados na camada de serviço *Premium*.

- Os bancos de dados criados depois que os recursos do OLTP Na Memória se tornaram ativos.
 - Um novo banco de dados não oferecerá suporte a OLTP Na Memória se ele for restaurado de um banco de dados criado antes que os recursos do OLTP Na Memória tenham se tornado ativos.


Em caso de dúvida, você também poderá executar o seguinte T-SQL SELECT para averiguar se seu banco de dados dá suporte o OLTP in-memory. Um resultado **1** significa que o banco de dados dá suporte a OLTP Na Memória:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Se a consulta retornar **1**, o OLTP in-memory terá suporte neste banco de dados, bem como em qualquer cópia do banco de dados e restauração de banco de dados criada com base nesse banco de dados.


#### Objetos permitidos apenas no Premium


Se um banco de dados contiver qualquer um dos seguintes tipos de objetos ou de tipos OLTP Na Memória, o downgrade da camada de serviço do banco de dados para Basic ou Standard não terá suporte. Para fazer o downgrade do banco de dados, primeiro descarte estes objetos:

- Tabelas com otimização de memória
- Tipos de tabela com otimização de memória
- Módulos compilados nativamente


#### Outros relacionamentos


- O uso de recursos OLTP in-memory com bancos de dados em pools elásticos não tem suporte durante a Preview.
 - Para mover um banco de dados que tenha ou já teve objetos OLTP in-memory para um pool elástico, siga estas etapas:
  - 1. Remover tabelas com otimização de memória, tipos de tabela e módulos do T-SQL compilados nativamente no banco de dados
  - 2. Alterar a camada de serviço do banco de dados para standard
  - 3. Mover o banco de dados para o pool elástico

- Usando OLTP Na Memória com o SQL Data Warehouse não tem suporte.
 - O recurso de índice columnstore da Análise Na Memória tem suporte no SQL Data Warehouse.

- O Armazenamento de Consulta não captura consultas dentro de módulos compilados nativamente.

- Alguns recursos Transact-SQL não têm suporte com o OLTP Na Memória. Isso se aplica ao Microsoft SQL Server e ao Banco de Dados SQL do Azure. Para obter mais informações, consulte:
 - [Suporte a Transact-SQL para OLTP em memória](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Construções Transact-SQL sem suporte do OLTP Na Memória](http://msdn.microsoft.com/library/dn246937.aspx)


## Próximas etapas


- Experimente [Usar o OLTP Na Memória em um Aplicativo SQL do Azure existente.](sql-database-in-memory-oltp-migration.md)


## Recursos adicionais

#### Informações mais detalhadas

- [Saiba mais sobre o OLTP Na Memória, que se aplica ao Microsoft SQL Server e ao Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre a Análise Operacional em Tempo Real no MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- White paper sobre [Padrões comuns de carga de trabalho e considerações sobre migração](http://msdn.microsoft.com/library/dn673538.aspx), que descreve os padrões de carga de trabalho onde o OLTP Na Memória geralmente fornece ganhos significativos de desempenho.

#### Design do aplicativo

- [OLTP Na Memória (Otimização Na Memória)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Usar o OLTP Na Memória em um Aplicativo do SQL Azure existente.](sql-database-in-memory-oltp-migration.md)

#### Ferramentas

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), para a versão mensal mais recente.

- [Descrição dos utilitários Replay Markup Language (RML) para o SQL Server](http://support.microsoft.com/pt-BR/kb/944837)

- [Monitorar o Armazenamento Na Memória](sql-database-in-memory-oltp-monitoring.md) para o OLTP Na Memória.

<!---HONumber=AcomDC_0831_2016-->