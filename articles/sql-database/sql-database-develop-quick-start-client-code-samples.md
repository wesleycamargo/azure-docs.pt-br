<properties 
	pageTitle="Amostras de código cliente de início rápido para o Banco de Dados SQL | Microsoft Azure" 
	description="Fornece exemplos de código e drivers para Node. js no Linux, Python no Mac OS, Java e Windows e muitos mais, tudo para clientes do Banco de Dados SQL."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="genemi"/>


# Exemplos de código do cliente de início rápido do Banco de Dados SQL


Este tópico fornece links para exemplos de código de início rápido que você pode usar para se conectar ao Banco de Dados SQL do Azure.


- Exemplos curtos de conexão e consulta.
- Repita os exemplos conexão e consulta, mas repita automaticamente se um erro encontrado for classificado como uma [*Falha transitória*](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors) (por exemplo, um tempo limite de conexão).


Os exemplos abrangem:


- Diversas linguagens de programação.
- Windows, Linux e Mac OS, como os sistemas operacionais que podem ser executados em seu programa cliente.
- Links para downloads drivers de conexão necessários.
- Exemplos de código curtos de início rápido.
- Mais exemplos que contêm tratamento de falha transitória na forma de lógica de repetição automática.
- Exemplos de código que convertem os conjuntos de resultados relacionais em um formato orientado ao objeto.


> [AZURE.NOTE]Exemplos de código para outras linguagens estão sendo preparados, e os links até eles serão adicionados a este tópico.


## Clientes no Linux


Esta seção fornece links para tópicos com exemplo de código para programas cliente que executam no Linux.


| Linguagem | Pequeno exemplo | Exemplo de repetição | Relacionado ao objeto |
| :-- | :-- | :-- | :-- |
| Node.js | [Entediante](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Clientes no Mac OS


Esta seção fornece links para tópicos com exemplo de código para programas cliente que executam no Mac OS.


| Linguagem | Pequeno exemplo | Exemplo de repetição | Relacionado ao objeto |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Clientes no Windows


Esta seção fornece links para tópicos com exemplo de código para programas cliente que executam no Windows.


| Linguagem | Pequeno exemplo | Exemplo de repetição | Relacionado ao objeto |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)<br/><br/>[ADO.NET com Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [Driver ODBC](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## Consulte também


- [Downloads de SDKs e ferramentas para várias plataformas e idiomas](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md)
- [Lista de códigos numéricos para erros transitórios](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)<br/>&nbsp;
- [Desenvolvimento do Banco de Dados SQL do Azure: tópicos sobre como fazer](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md)
- [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md)

<!---HONumber=July15_HO4-->