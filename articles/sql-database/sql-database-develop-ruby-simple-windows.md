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
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Conectar-se ao Banco de Dados SQL usando Ruby no Windows

[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico apresenta um exemplo de código Ruby que é executado em um computador Windows executando Windows 8.1 para se conectar a um Banco de Dados SQL.

## Instalar os módulos necessários

Abra seu terminal e instale o seguinte:

**1) Ruby:** se seu computador não tiver o Ruby, instale-o. Para novos usuários do Ruby, recomendamos que você use instaladores Ruby 2.1.x. Eles fornecem uma linguagem estável e uma lista extensa de pacotes (gemas) que são compatíveis e atualizados. [Vá para a página de download do Ruby](http://rubyinstaller.org/downloads/) e baixe o instalador 2.1.x apropriado. Por exemplo, se você estiver em um computador de 64 bits, baixe o instalador do **Ruby 2.1.6 (x64)**. <br/><br/>Depois de ter baixado o instalador, faça o seguinte:


- Clique duas vezes no arquivo para iniciar o instalador.

- Selecione seu idioma e concorde com os termos.

- Na tela de configurações de instalação, selecione as caixas de seleção ao lado de ambos *Adicionar executáveis do Ruby ao seu caminho* e *Associar arquivos .rb e .rbw a esta instalação do Ruby*.


**2) DevKit:** Baixar DevKit da [página do RubyInstaller](http://rubyinstaller.org/downloads/)

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

## Criar um banco de dados e recuperar a cadeia de conexão

O exemplo do Ruby se baseia em dados de exemplo do `AdventureWorks`. Se ainda não tiver o `AdventureWorks`, você pode ver como criá-lo visitando [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

O tópico também explica como recuperar a cadeia de conexão do banco de dados.

## Conectar-se ao seu Banco de Dados SQL

A função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) é usada para se conectar ao Banco de Dados SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Executar uma instrução SELECT e recuperar o conjunto de resultados

Copie e cole o código a seguir em um arquivo vazio. Nomeie-o como test.rb. Em seguida, execute-o digitando o seguinte comando no seu prompt de comando:

	ruby test.rb

No exemplo de código, A função [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) é usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essa função aceita uma consulta e retorna um conjunto de resultados. O conjunto de resultados é iterado usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

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

Para usar o TinyTDS com o Azure, é recomendável que você execute várias instruções `SET` para alterar o modo como a sessão atual lida com informações específicas. Instruções `SET` recomendadas são fornecidas no exemplo de código Por exemplo, `SET ANSI_NULL_DFLT_ON` permitirá que novas colunas sejam criadas para permitir valores nulos, mesmo que o status de nulidade da coluna não seja declarado explicitamente.

Para alinhar com o formato [datetime](http://msdn.microsoft.com/library/ms187819.aspx) do Microsoft SQL Server, use a função [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) a ser convertida no formato de data e hora correspondente.

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

<!---HONumber=August15_HO8-->