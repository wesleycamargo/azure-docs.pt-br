<properties 
	pageTitle="Usar o Banco de Dados SQL do .NET (C#)" 
	description="Use o código de exemplo neste início rápido para criar um aplicativo moderno com C# e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="tobbox" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="tobiast"/>


# Usando o Banco de Dados SQL do .NET (C#) 


[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Requisitos

### .NET Framework

O .NET Framework é pré-instalado no Windows. Para Linux e Mac OS X, você pode baixar o .NET Framework a partir de [Projeto Mono](http://www.mono-project.com/).

### Um Banco de Dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo e obter a cadeia de conexão.

## Conectar-se ao seu Banco de Dados SQL

A [classe System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) é usada para se conectar ao Banco de Dados SQL.
	
	```
	using System.Data.SqlClient;
	
	class Sample
	{
	  static void Main()
	  {
		  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		  {
			  conn.Open();	
		  }
	  }
	}	
	```

## Executar uma consulta e recuperar o conjunto de resultados 

É possível usar as classes [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) e [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Observe que System.Data.SqlClient também oferece suporte à recuperação de dados em um [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) offline.
	
	```
	using System;
	using System.Data.SqlClient;
	
	class Sample
	{
		static void Main()
		{
		  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
			{
				var cmd = conn.CreateCommand();
				cmd.CommandText = @"
						SELECT 
							c.CustomerID
							,c.CompanyName
							,COUNT(soh.SalesOrderID) AS OrderCount
						FROM SalesLT.Customer AS c
						LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
						GROUP BY c.CustomerID, c.CompanyName
						ORDER BY OrderCount DESC;";
	
				conn.Open();	
			
				using(var reader = cmd.ExecuteReader())
				{
					while(reader.Read())
					{
						Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
					}
				}					
			}
		}
	}
	
	```

## Inserir uma linha, passar parâmetros e recuperar o valor de chave primária gerado 

No Banco de Dados SQL, a propriedade [IDENTIDADE](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUÊNCIA](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente os valores de [chave primária](https://msdn.microsoft.com/library/ms179610.aspx). Neste exemplo, você verá como executar uma [instrução insert](https://msdn.microsoft.com/library/ms174335.aspx), passar parâmetros com segurança que protegem contra [injeção de SQL](https://msdn.microsoft.com/magazine/cc163917.aspx) e recuperar o valor de chave primária gerado automaticamente.

O método [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) na classe [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) pode ser usado para executar uma instrução e recuperar a primeira coluna e linha retornadas por essa instrução. A cláusula [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx) da instrução INSERT pode ser usada para retornar os valores inseridos como um conjunto de resultados para o aplicativo de chamada. Observe que OUTPUT também é compatível com as instruções [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) e [MERGE](https://msdn.microsoft.com/library/bb510625.aspx) instruções. Caso seja inserida mais de uma linha, você deve usar o método [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) para recuperar os valores inseridos para todas as linhas.
	
	```
	class Sample
	{
	    static void Main()
	    {
			using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	        {
	            var cmd = conn.CreateCommand();
	            cmd.CommandText = @"
	                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
	                OUTPUT INSERTED.ProductID
	                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";
	
	            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
	            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
	            cmd.Parameters.AddWithValue("@Cost", 0);
	            cmd.Parameters.AddWithValue("@Price", 0);
	
	            conn.Open();
	
	            int insertedProductId = (int)cmd.ExecuteScalar();
	
	            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
	        }
	    }
	}
	```

<!---HONumber=58--> 