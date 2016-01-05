<properties
	pageTitle="Conectar ao banco de dados SQL usando o Ruby com TinyTDS no Windows"
	description="Dê um exemplo de código Ruby que você pode executar no Windows para se conectar ao Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="mebha"/>


# Conectar-se ao Banco de Dados SQL usando Ruby no Windows

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico apresenta um exemplo de código Ruby que é executado em um computador Windows executando Windows 8.1 para se conectar a um Banco de Dados SQL.

## Pré-requisitos

###Instalar os módulos necessários

Abra seu terminal e instale o seguinte:

**1) Ruby:** se seu computador não tiver o Ruby, instale-o. Para novos usuários do Ruby, recomendamos que você use instaladores Ruby 2.1.x. Eles fornecem uma linguagem estável e uma lista extensa de pacotes (gemas) que são compatíveis e atualizados. [Vá para a página de download do Ruby](http://rubyinstaller.org/downloads/) e baixe o instalador 2.1.x apropriado. Por exemplo, se você estiver em um computador de 64 bits, baixe o instalador do **Ruby 2.1.6 (x64)**. <br/><br/>Depois de ter baixado o instalador, faça o seguinte:


- Clique duas vezes no arquivo para iniciar o instalador.

- Selecione seu idioma e concorde com os termos.

- Na tela de configurações de instalação, selecione as caixas de seleção ao lado de ambos *Adicionar executáveis do Ruby ao seu caminho* e *Associar arquivos .rb e .rbw a esta instalação do Ruby*.


**2) DevKit:** baixar DevKit da [página do RubyInstaller](http://rubyinstaller.org/downloads/)

Depois que o download for concluído, faça o seguinte:


- Clique duas vezes no arquivo. Será solicitado que você indique onde extrair os arquivos.

- Clique no botão "..." e selecione "C:\\DevKit". Você provavelmente precisará criar essa pasta primeiro, clicando em "Criar Nova Pasta".

- Clique em "OK" e depois em "Extrair" para extrair os arquivos.


Agora abra um prompt de comando e digite os seguintes comandos:

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Agora você tem um Ruby totalmente funcional e RubyGems!


**3) TinyTDS**: navegue até C:\\DevKit e execute o comando a seguir do seu terminal. Isso instalará o TinyTDS em seu computador.

	gem inst tiny_tds --pre

### Um Banco de Dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.


## Etapa 1: Obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 2: Conectar

A função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) é usada para se conectar ao Banco de Dados SQL.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Etapa 3: Executar uma consulta

Copie e cole o código a seguir em um arquivo vazio. Nomeie-o como test.rb. Em seguida, execute-o digitando o seguinte comando no seu prompt de comando:

	ruby test.rb

No exemplo de código, A função [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) é usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essa função aceita uma consulta e retorna um conjunto de resultados. O conjunto de resultados é iterado usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## Etapa 4: Inserir uma linha

Nesse exemplo, você verá como executar uma instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) com segurança, passar parâmetros que protegem seu aplicativo contra vulnerabilidade [a injeção de SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e recuperar o valor da [Chave Primária](https://msdn.microsoft.com/library/ms179610.aspx) gerado automaticamente.

Para usar o TinyTDS com o Azure, é recomendável que você execute várias instruções `SET` para alterar como a sessão atual lida com informações específicas. As instruções `SET` recomendadas são fornecidas no exemplo de código. Por exemplo, `SET ANSI_NULL_DFLT_ON` permitirá novas colunas criadas para permitir valores nulos, mesmo que o status de nulidade da coluna não seja declarado explicitamente.

Para alinhar com o formato [datetime](http://msdn.microsoft.com/library/ms187819.aspx) do Microsoft SQL Server, use a função [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) a ser convertida para o formato de datetime correspondente.

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

<!---HONumber=AcomDC_1210_2015-->