<properties 
	pageTitle="Conectar ao banco de dados SQL usando o Ruby com TinyTDS no Ubuntu" 
	description="Dê um exemplo de código Ruby que você pode executar como um cliente no Ubuntu Linux para se conectar ao Banco de Dados SQL do Azure."
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
	ms.date="09/21/2015" 
	ms.author="andrela"/>


# Conectar-se ao Banco de Dados SQL usando Ruby no Ubuntu Linux

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico apresente um exemplo de código Ruby executado em um computador cliente Ubuntu Linux, para se conectar a um Banco de Dados SQL do Azure.

## Instalar os módulos necessários

Abra seu terminal e instale o FreeTDS se você não tiver em seu computador.
	
    sudo apt-get --assume-yes update 
    sudo apt-get --assume-yes install freetds-dev freetds-bin

Depois que seu computador estiver configurado com o FreeTDS, instale o Ruby se você ainda não tiver em seu computador.
    
    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev 
    curl -L https://get.rvm.io | bash -s stable

Se você tiver problemas com assinaturas, execute o seguinte comando.

    command curl -sSL https://rvm.io/mpapis.asc | gpg --import - 

Se não houver nenhum problema com assinaturas, execute os seguintes comandos.

    source ~/.rvm/scripts/rvm 
    rvm install 2.1.2 
    rvm use 2.1.2 --default 
    ruby -v 

Certifique-se de que você está executando a versão 2.1.2 ou a VM do Ruby.

Em seguida, instale o TinyTDS.

    gem install tiny_tds

## Criar um banco de dados e recuperar a cadeia de conexão

O exemplo Ruby se baseia em dados de exemplo do AdventureWorks. Se você ainda não tiver o AdventureWorks, você pode ver como criá-lo no tópico a seguir: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md)

O tópico também explica como recuperar a cadeia de conexão do banco de dados.

## Conectar-se ao seu Banco de Dados SQL

A função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) é usada para se conectar ao banco de dados SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Executar uma instrução SELECT e recuperar o conjunto de resultados

A função [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) é usada para recuperar um conjunto de resultados de uma consulta no banco de dados SQL. Essa função aceita uma consulta e retorna um conjunto de resultados. O conjunto de resultados é iterado usando [result.each |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## Inserir uma linha, passar parâmetros e recuperar o valor de chave primária gerado

O exemplo de código:

- Passa parâmetros de valores a serem inseridos em uma linha.
- Insere a linha.
- Recupera o valor que foi gerado para a chave primária.

No Banco de Dados SQL, a propriedade [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente [valores de chave primária](http://msdn.microsoft.com/library/ms179610.aspx).

Para usar o TinyTDS com o Azure, é recomendável que você execute várias `SET` instruções para alterar como a sessão atual lida com informações específicas. Instruções `SET` recomendadas são fornecidas no exemplo de código. Por exemplo, `SET ANSI_NULL_DFLT_ON` permitirá novas colunas criadas para permitir valores nulos, mesmo que o status de nulidade da coluna não seja declarado explicitamente.

Para alinhar com o formado [datetime](http://msdn.microsoft.com/library/ms187819.aspx) do Microsoft SQL Server, use a função [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) a ser convertida para o formato de data e hora correspondente.

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

<!---HONumber=Nov15_HO2-->