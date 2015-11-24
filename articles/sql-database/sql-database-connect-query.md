<properties
	pageTitle="Conectar-se ao Banco de Dados SQL com uma consulta C# | Microsoft Azure"
	description="Escreva um programa em C# para consultar um Banco de Dados SQL e se conectar a ele Informações sobre endereços IP, cadeias de conexão, logon seguro e Visual Studio gratuito."
	services="sql-database"
	keywords="consulta de banco de dados c#, consulta c#, conectar-se ao banco de dados"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="genemi"/>


# Escreva um programa em C&#x23; para consultar um Banco de Dados SQL e se conectar a ele

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como escrever um programa em C# para consultar um banco de dados SQL do Azure na nuvem e como se conectar a ele.

Este artigo descreve todas as etapas para as pessoas que não tenham experiência no Banco de Dados SQL do Azure, em C# e em ADO.NET. Outras pessoas experientes no Microsoft SQL Server e em C# poderão ignorar algumas etapas e se concentrar nas informações específicas do Banco de Dados SQL.


## Pré-requisitos


Para executar o exemplo de código de consulta C#, você deverá ter:


- Uma conta e uma assinatura do Azure. Você pode se inscrever em uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).


- Um banco de dados de demonstração **AdventureWorksLT** no serviço Banco de Dados SQL do Azure.
 - [Crie o banco de dados de demonstração](sql-database-get-started.md) em minutos.


- Visual Studio 2013 - atualização 4 (ou posterior). A Microsoft agora oferece a Comunidade do Visual Studio *gratuitamente*.
 - [Comunidade do Visual Studio, download](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Mais opções para o Visual Studio gratuito](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Ou veja como a próxima [etapa](#InstallVSForFree) deste tópico descreve como o [portal de visualização do Azure](http://portal.azure.com/) orienta você pela instalação do Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Etapa 1: Instalar a Comunidade do Visual Studio gratuitamente


Caso seja necessário instalar o Visual Studio, você poderá:

- Instalar a Comunidade do Visual Studio gratuitamente ao usar seu navegador para acessar as páginas da Web do produto Visual Studio que ofereçam downloads gratuitos e outras opções ou
- Permitir que o [portal de visualização do Azure](http://portal.azure.com/) oriente você na página de download, que será descrita a seguir.


### Visual Studio por meio do portal de visualização do Azure


1. Faça logon por meio do [portal de visualização do Azure](http://portal.azure.com/), em http://portal.azure.com/.

2. Clique em **PROCURAR* TUDO** > **Bancos de dados SQL**. Será aberta uma folha para a pesquisa de bancos de dados.

3. Na caixa de texto de filtro próxima à parte superior, comece a digitar o nome do seu banco de dados **AdventureWorksLT**.

4. Quando a linha do banco de dados for exibida no servidor, clique na linha. Será aberta uma folha para seu banco de dados.

5. Para sua conveniência, clique no controle de minimização em cada uma das folhas anteriores.

6. Clique no botão **Abrir no Visual Studio** próximo à parte superior da folha de seu banco de dados. Será aberta uma nova folha do Visual Studio, com links para locais de instalação do Visual Studio.

	![Botão Abrir no Visual Studio][20-OpenInVisualStudioButton]

7. Clique no link **Comunidade (gratuita)** ou em um link semelhante. Uma nova página da Web é adicionada.

8. Use os links na nova página da Web para instalar o Visual Studio.

9. Depois que o Visual Studio estiver instalado, na folha **Abrir no Visual Studio**, clique no botão **Abrir no Visual Studio**. O Visual Studio abre.

10. Para o benefício de seu painel **Pesquisador de Objetos do SQL Server**, o Visual Studio solicita que você preencha os campos de cadeia de conexão em uma caixa de diálogo.
 - Escolha **Autenticação do SQL Server**, e não **Autenticação do Windows**.
 - Lembre-se de especificar o banco de dados **AdventureWorksLT** (**Opções** > **Propriedades de Conexão** na caixa de diálogo).

11. No **Pesquisador de Objetos do SQL Server**, expanda o nó do seu banco de dados.


## Etapa 2: Criar um novo projeto no Visual Studio


No Visual Studio, crie um novo projeto baseado no modelo inicial para C# > Windows > **Aplicativo de Console**.


1. Clique em **Arquivo** > **Novo** > **Projeto**. A caixa de diálogo **** é exibida.

2. Em **Instalado**, expanda para C# e para o Windows de forma que a opção **Aplicativo de Console** apareça no painel do meio.

	![A caixa de diálogo Novo Projeto][30-VSNewProject]

2. Em **Nome**, insira **ConnectAndQuery\_Example**. Clique em **OK**.


## Etapa 3: Adicionar uma referência de assembly ao processamento de configuração


Nosso exemplo de C# usa o assembly do .NET Framework **System.Configuration.dll** e , portanto, vamos adicionar uma referência a ele.


1. No painel **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** > **Adicionar Referência**. A janela **Gerenciador de Referências** é exibida.

2. Expanda **Assemblies** > **Estrutura**.

3. Role e então clique para realçar **System.Configuration**. Verifique se a caixa de seleção dele está marcada.

4. Clique em **OK**.

5. Compile seu programa pelo menu **COMPILAR** > **Compilar Solução**.


## Etapa 4: Obter a cadeia de conexão


Use o [portal de visualização do Azure](http://portal.azure.com/) para copiar a cadeia de conexão necessária para a conexão ao banco de dados SQL.

Sua primeira utilização será para conectar o Visual Studio ao Banco de Dados SQL do Azure **AdventureWorksLT**.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## Etapa 5: Adicionar a cadeia de conexão ao arquivo App.config


1. No Visual Studio, abra o arquivo App.config do painel Gerenciador de soluções.

2. Adicione um elemento **&#x3c;configuração&#x3e; &#x3c;/configuração&#x3e;** como mostrado no exemplo de código no App.config de exemplo a seguir.
 - Substitua os *{seus\_espaços\_reservados}* pelos valores reais:

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. Salve a alteração feita em App.config.

4. No painel Gerenciador de Soluções, clique com o botão direito do mouse no nó **App.config** e clique em **Propriedades**.

5. Defina **Copiar para Diretório de Saída** como **Copiar sempre**.
 - Isso faz com que o conteúdo do arquivo App.config substitua o conteúdo do arquivo &#x2a;.exe.config no diretório onde o arquivo &#x2a;.exe foi compilado. A substituição ocorrerá sempre que você recompilar &#x2a;.exe.
 - O arquivo &#x2a;.exe.config será lido quando o nosso programa C# de exemplo for executado.

	![Copiar para o Diretório de Saída = Copiar sempre][50-VSCopyToOutputDirectoryProperty]


## Etapa 6: Colar o código de exemplo C#


1. No Visual Studio, use o painel **Gerenciador de Soluções** para abrir o arquivo **Program.cs**.

	![Cole o código de consulta C# de exemplo.][40-VSProgramCsOverlay]

2. Substitua todo o código inicial em Program.cs colando o seguinte código de exemplo C#.
 - Se você quiser um exemplo de código menor, poderá atribuir toda a cadeia de conexão como um literal à variável **SQLConnectionString**. Em seguida, você poderá apagar os dois métodos **GetConnectionStringFromExeConfig** e **GatherPasswordFromConsole**.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### Compilar seu programa


1. No Visual Studio, compile seu programa clicando no menu **Compilar** > **Compilar Solução**.


### Resumo das ações no programa de exemplo


1. Lê a maior parte da cadeia de conexão SQL de um arquivo de configuração.

2. Reúne o nome de usuário e a senha do teclado e os adiciona para completar a cadeia de conexão.

3. Usa a cadeia de conexão e classes do ADO.NET para conectar-se ao banco de dados de demonstração **AdventureWorksLT** no Banco de Dados SQL.

4. Emite um **SELECT** SQL para ler a partir da tabela **SalesLT**.

5. Imprime as linhas retornadas no console.


Tentamos manter o exemplo C# pequeno. Ainda assim, adicionamos código para ler um arquivo de configuração para atendermos a várias solicitações de clientes como você. Nós concordamos que os programas de qualidade de produção devem usar arquivos de configuração em vez de literais codificados no .exe.


> [AZURE.WARNING]Para fins de brevidade do código, optamos por não incluir o código de manipulação de exceção e de lógica de repetição neste exemplo educativo. No entanto, os programas de produção que interagem com um banco de dados de nuvem devem incluir ambos.
>
> [Este](sql-database-develop-csharp-retry-windows.md) é um link para um exemplo de código com lógica de repetição.


## Etapa 7: Adicionar o intervalo de endereços IP permitidos no firewall do servidor


O programa cliente C# não poderá se conectar ao Banco de Dados SQL até que o endereço IP do computador cliente tenha sido adicionado ao firewall do Banco de Dados SQL. O programa falhará com uma mensagem de erro útil que indica o endereço IP necessário.


Você pode usar o [portal de visualização do Azure](http://portal.azure.com/) para adicionar o endereço IP.



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



Para saber mais, consulte <br/>[Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md)



## Etapa 8: Executar o programa


1. No Visual Studio, execute o programa de consulta C# pelo menu **DEPURAR** > **Iniciar Depuração**. Uma janela de console é exibida.

2. Insira seu nome de usuário e senha, como indicado.
 - Algumas ferramentas de conexão exigem seu nome de usuário para anexarem “@{seu\_nomeDeServidor\_aqui}”, mas para o ADO.NET, esse sufixo é opcional. Não se preocupe em digitar o sufixo.

3. As linhas de dados são exibidas.


## Links relacionados


- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)

- Se o seu programa cliente for executado em uma VM do Azure, saiba mais sobre as portas TCP diferentes de 1433 em:<br/>[Portas além de 1433 para o ADO.NET 4.5 e o Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=Nov15_HO4-->