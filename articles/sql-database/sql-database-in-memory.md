<properties
	pageTitle="Introdução ao SQL Na Memória | Microsoft Azure"
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
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Introdução a Na Memória (visualização)


As tecnologias Na Memória do SQL melhoram muito o desempenho de cargas de trabalho transacionais e analíticas. Com o OLTP Na Memória, você pode atingir até 30 vezes o ganho em produtividade de transações, e com a Análise Na Memória, você pode atingir uma melhoria de até 100 vezes no desempenho da consulta, dependendo da carga de trabalho.

A Análise Em Memória está disponível de forma geral. O OLTP Na Memória está na versão de visualização para bancos de dados SQL do Azure Premium.


## Introdução

Experimente o OLTP Na Memória para cargas de trabalho transacionais:


- [Enriquecer um banco de dados AdventureWorksLT com exemplos de OLTP Na Memória](#Enrich-an-AdventureWorksLT-database-with-In-Memory-OLTP-samples).
- [Usar o OLTP Na Memória em um Aplicativo do SQL Azure existente.](sql-database-in-memory-oltp-migration.md)
- [Monitorar o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md).


Experimente a Análise Na Memória para cargas de trabalho transacionais:

- [Instalar a Análise Na Memória](#Install-the-In-Memory-Analytics-sample).
- Saiba mais sobre [índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx) no MSDN.


## Enriquecer um banco de dados AdventureWorksLT com exemplos de OLTP Na Memória

Você pode criar o banco de dados de exemplo AdventureWorksLT [V12] com alguns cliques no portal de visualização do Azure. Em seguida, as etapas a seguir explicam como é possível enriquecer seu banco de dados AdventureWorksLT com tabelas e procedimentos armazenados compilados nativamente, para ilustrar os objetos OLTP Na Memória.


1. No [portal de visualização do Azure](https://portal.azure.com/), crie um banco de dados Premium em um servidor V12. Defina Origem como o banco de dados de exemplo AdventureWorksLT[V12].
 - Para obter instruções mais detalhadas sobre esta etapa, consulte [neste artigo](sql-database-get-started.md).

2. Conecte-se ao banco de dados com o [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) ou com um utilitário semelhante.

3. Copie o [script OLTP Na Memória](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) para a área de transferência.
 - O script cria os objetos necessários Na Memória no banco de dados de exemplo AdventureWorksLT criado na etapa 1.

4. Cole o script Transact-SQL no SSMS.exe e execute o script.



&nbsp;

O exemplo contém as seguintes tabelas com otimização de memória:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Inspecione as tabelas com otimização de memória por meio do Pesquisador de objetos ou de consultas de exibição de catálogo.

Exemplo:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


Da mesma forma, o procedimento armazenado compilado nativamente saleslt.usp\_insertsalesorder\_inmem poderá ser inspecionado por meio do Pesquisador de Objetos ou de consultas das exibições do catálogo.

Exemplo:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## Executar a carga de trabalho de exemplo

Use procedimentos armazenados SalesLT.usp\_InsertSalesOrder\_inmem e SalesLT.usp\_InsertSalesOrder\_ondisk para comparar o desempenho de insert entre as tabelas com otimização de memória versus as baseadas em disco.

Recomendamos a execução da carga de trabalho usando um número de conexões de cliente simultâneas de um aplicativo que reside na mesma região do Azure que o banco de dados de exemplo.

### Inserção de pedido de vendas de exemplo

O script a seguir insere um pedido de vendas de exemplo com cinco itens de linha nas tabelas com otimização de memória SalesLT.SalesOrderHeader\_inmem e SalesLT.SalesOrderDetail\_inmem:


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

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### Executando a carga de trabalho de estresse de exemplo

Para executar uma carga de trabalho de estresse de exemplo, crie uma [Máquina Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região do banco de dados de exemplo. Use a ferramenta de linha de comando ostress para executar a carga de trabalho. Você poderá encontrar as instruções para [instalar e executar o ostress](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx) no MSDN.

Quando executado Prompt de Comando do RML, o comando a seguir insere um milhão de pedidos de vendas, com cinco itens de linha de cada um, nas tabelas com otimização de memória, usando 100 conexões simultâneas:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


Substitua <servername> pelo nome do seu servidor, <database> pelo nome do banco de dados e <login> e <password> pelas informações de logon.

Para comparar o desempenho de inserção de tabelas com otimização de memória com o de tabelas tradicionais baseadas em disco, use o comando a seguir para inserir os mesmos um milhão de pedidos de vendas em tabelas baseadas em disco:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


Testes mostraram um aprimoramento de desempenho em torno de nove vezes melhor em comparação com as tabelas baseadas em disco para esta carga de trabalho, com o ostress sendo executado em uma VM na mesma região do Azure que o banco de dados.

Após a execução de cada teste, redefina o exemplo, para evitar a falta de espaço de armazenamento na memória. Execute a instrução T-SQL a seguir em seu banco de dados. Uma única execução de teste inserindo um milhão de resultados de pedidos de vendas em mais de 500 MB de dados em tabelas com otimização de memória.


```
EXECUTE Demo.usp_DemoReset;
```


## Instalar o exemplo de Análise Na Memória

**Primeiro**, crie um novo Banco de Dados SQL do Azure.

- Escolha qualquer edição Premium (a versão Premium é necessária para o Columnstore)
- Crie o banco de dados a partir do Exemplo.

- Para simplificar, nomeie o banco de dados como AdventureworksLT



**Em seguida**, conecte-se ao novo Banco de Dados SQL do Azure por meio da versão de setembro de 2015 Preview do [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) ou posterior.


- Executar o Script de Instalação [sql\_in memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql)


- Crie o esquema necessário para as tabelas de dimensões e fatos executando o script. O script criará duas tabelas de fatos com cerca de 3,5 milhões de linhas cada.


- FactResellerSales\_CCI, que tem uma tabela columnstore


- FactResellerSalesXL\_PageCompressed, que é uma tabela de árvore B equivalente que é compactada por página. **Observação:** este script pode levar até 15 minutos para executar e gerar os dados.


**Execute** as consultas de demonstração no arquivo [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql). e explore o recurso.

## Mais sobre OLTP Na Memória

[OLTP Na Memória (Otimização Na Memória)](https://msdn.microsoft.com/library/dn133186.aspx)

[White paper sobre padrões comuns de carga de trabalho e considerações sobre migração](https://msdn.microsoft.com/library/dn673538.aspx) descreve os padrões de carga de trabalho onde o OLTP Na Memória geralmente fornece ganhos significativos de desempenho.

## Considerações de visualização

**Só** há suporte para o OLTP Na Memória para bancos de dados Premium.

Só há suporte para o OLTP Na Memória em bancos de dados recém-criados. Não há suporte em bancos de dados criados com base em um banco de dados existente por meio da funcionalidade de cópia ou de restauração. No entanto, assim que você tiver o novo banco de dados, poderá copiar ou restaurar esse banco de dados e manter a funcionalidade completa do OLTP na memória.

Para verificar se o OLTP Na Memória tem suporte em um determinado banco de dados, execute a seguinte consulta:


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


Se a consulta retornar **1**, o OLTP Na Memória tem suporte neste banco de dados, bem como em qualquer cópia do banco de dados e restauração de banco de dados criada com base nesse banco de dados.

Se um banco de dados contiver qualquer um dos seguintes tipos de objetos ou tipos, a alteração da camada de serviço do banco de dados para básico ou padrão não terá suporte. Para fazer o downgrade do banco de dados, primeiro descarte os objetos.

- Tabelas com otimização de memória
- Tipos de tabela com otimização de memória
- Os módulos compilados nativamente Usando OLTP Na Memória com bancos de dados em pools elásticos não têm suporte.

Usando OLTP Na Memória com o SQL Data Warehouse não tem suporte.

O Armazenamento de Consulta não captura consultas dentro de módulos compilados nativamente.

Alguns recursos Transact-SQL não têm suporte com o OLTP Na Memória. Para obter detalhes, consulte [Suporte do Transact-SQL para OLTP Na Memória](https://msdn.microsoft.com/library/dn133180.aspx).

## Ferramentas com suporte

Versão de setembro de 2015 de visualização do [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) ou posterior.

Versão de setembro de 2015 de visualização do [SQL Server Data Tools Preview (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) ou posterior.

## Recursos adicionais

Mais informações sobre o OLTP Na Memória e Análise: [OLTP Na Memória (Otimização Na Memória)](https://msdn.microsoft.com/library/dn133186.aspx)

[Saiba mais sobre índices Columnstore no MSDN](https://msdn.microsoft.com/library/gg492088.aspx)

[White paper sobre padrões comuns de carga de trabalho e considerações sobre migração](https://msdn.microsoft.com/library/dn673538.aspx) descreve os padrões de carga de trabalho onde o OLTP Na Memória geralmente fornece ganhos significativos de desempenho.

## Próximas etapas

Experimente [Usar o OLTP Na Memória em um Aplicativo do SQL Azure existente.](sql-database-in-memory-oltp-migration.md)

[Monitore o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md) para o OLTP Na Memória.

<!---HONumber=Nov15_HO1-->