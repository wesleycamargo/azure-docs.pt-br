<properties
	pageTitle="Conectar ao Banco de Dados SQL usando o Ruby com TinyTDS no Mac OS X (Yosemite)"
	description="Dê um exemplo de código Ruby que você pode executar no Mac OS X (Yosemite) para se conectar ao Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="andrela"/>


# Conectar ao Banco de Dados SQL usando o Ruby no Mac OS X (Yosemite)

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico apresenta um exemplo de código Ruby que é executado em computadores Mac com o Yosemite para se conectar a um Banco de Dados SQL do Azure.

## Etapa 1: configurar o Ambiente de Desenvolvimento

[Prerequisites for using the TinyTDS Ruby Driver for SQL Server](https://msdn.microsoft.com/library/mt711041.aspx#Mac)

## Etapa 2: Criar um banco de dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.


## Etapa 3: obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 4: conectar

A função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) é usada para se conectar ao Banco de Dados SQL.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Etapa 5: executar uma consulta

A função [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) é usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essa função aceita uma consulta e retorna um conjunto de resultados. O conjunto de resultados é iterado usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("select * from SalesLT.Product")
    results.each do |row|
    puts row
    end

## Etapa 6: inserir uma linha

Nesse exemplo, você verá como executar uma instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) com segurança, passar parâmetros que protegem seu aplicativo contra vulnerabilidade [a injeção de SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e recuperar o valor da [Chave Primária](https://msdn.microsoft.com/library/ms179610.aspx) gerado automaticamente.


Para usar o TinyTDS com o Azure, recomendamos a execução de várias `SET` instruções para alterar o modo como a sessão atual lida com informações específicas. Instruções `SET` recomendadas são fornecidas no código de exemplo. Por exemplo, `SET ANSI_NULL_DFLT_ON` permitirá novas colunas criadas para permitir valores nulos, mesmo que o status de nulidade da coluna não seja declarado explicitamente.

Para alinhar com o formato [datetime](http://msdn.microsoft.com/library/ms187819.aspx) do Microsoft SQL Server, use a função [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) a ser convertida para o formato de data e hora correspondente.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row|
    puts row
    end

<!-----------HONumber=AcomDC_0330_2016-->