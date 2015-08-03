<properties
	pageTitle="Conectar-se ao Banco de Dados SQL e faça uma consulta com C#"
	description="Exemplo de código para um cliente C# usando o ADO.NET para conectar e interagir com o banco de dados do AdventureWorks no serviço de nuvem do Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="ckarst"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="07/17/2015"
	ms.author="cakarst"/>


# Conectar-se ao Banco de Dados SQL e faça uma consulta com C&#x23;


Este tópico fornece um exemplo de código C# que mostra como se conectar a um Banco de Dados SQL do AdventureWorks existente usando o ADO.NET. O exemplo é compilado em um aplicativo de console que consulta o banco de dados e exibe o conjunto de resultados.


## Pré-requisitos


- Um banco de dados do AdventureWorks existente no banco de dados SQL do Azure. [Criar um em minutos](sql-database-get-started.md).
- [Visual Studio com o .NET Framework](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)


## Etapa 1: aplicativo de console


1. No Visual Studio, crie um aplicativo de console C#.


![Conectar e consultar](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## Etapa 2: exemplo de código SQL


1. Copie e cole o exemplo de código abaixo em seu aplicativo de console.


> [AZURE.WARNING]O exemplo de código é projetado para ser o mais curto possível a fim de facilitar a compreensão. O exemplo não deve ser usado em produção.


Esse código não deve ser usado em produção. Se você quiser implementar o código de produção pronto, considere as seguintes práticas recomendadas do setor:


- Manipulação de exceção.
- Lógica de repetição para erros transitórios.
- Armazenamento seguro de senhas em um arquivo de configuração.



### Código-fonte de exemplo do C#


Cole este código-fonte em seu arquivo **Program.cs**.


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;

	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## Etapa 3: localizar a cadeia de conexão do banco de dados


1. Abra o [portal de visualização do Azure](http://portal.azure.com/).
2. Clique em **Procurar** > **Banco de Dados SQL** > **Banco de dados do "Adventure Works"**> **Propriedades** > **Mostrar cadeias de conexão de banco de dados**.


![Portal](./media/sql-database-connect-query/ConnectandQuery_portal.png)


Na folha de cadeias de conexão de banco de dados, é possível localizar as cadeias de conexão apropriadas para ADO.NET, ODBC, PHP e JDBC.


## Etapa 4: substituir as informações reais de conexão


- No código-fonte que você colou, substitua o espaço reservado *[Your_Connection_String]* pela cadeia de conexão e não se esqueça de substituir *your_password_here* nessa cadeia pela sua senha real.


## Etapa 5: executar o aplicativo


1. Para compilar e executar o aplicativo, clique em **DEPURAR** > **Iniciar depuração**
2. O programa imprime os resultados da consulta na janela do console.
 

<!---HONumber=July15_HO4-->