<properties 
	pageTitle="Lógica de repetição em C# para se conectar ao Banco de Dados SQL | Microsoft Azure" 
	description="O exemplo em C# inclui uma lógica de repetição para interagir de forma confiável com o Banco de Dados SQL do Azure." 
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
	ms.date="10/01/2015" 
	ms.author="genemi"/>


# Exemplo de código: repetir a lógica em C# para se conectar ao Banco de Dados SQL


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico fornece um exemplo de código C# que demonstra uma lógica de repetição personalizada. A lógica de repetição foi criada para processar normalmente erros temporários ou *falhas transitórias* que tendem a desaparecer se o programa aguardar alguns segundos e tentar novamente.


Classes ADO.NET que você usa para se conectar ao Microsoft SQL Server local também podem se conectar ao Banco de Dados SQL Azure. No entanto, por si só, as classes ADO.NET não podem fornecer toda a solidez e confiabilidade necessárias para o uso na produção. O programa cliente pode encontrar falhas transitórias das quais ele deve se recuperar silenciosamente por conta própria.


Entre alguns exemplos de falhas transitórias estão:


- Uma conexão pela Internet está sujeita a breves interrupções de rede, após as quais a conexão pode ser recriada.

- A computação em nuvem envolve o balanceamento de carga, que pode rapidamente bloquear tentativas de conexão ou de consulta.


## Identificar erros transitórios


O programa deve distinguir entre erros transitórios versus erros persistentes. Se o programa tiver um erro de ortografia no nome do banco de dados de destino, o erro "Nenhum banco de dados encontrado" persistirá e ocorrerá novamente sempre que você executar novamente o programa.


A lista com os números dos erros que são categorizados como falhas transitórias está disponível em:


- [Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Veja a seção superior sobre *Erros transitórios e erros de perda de conexão*.


## Exemplo de código C#


O exemplo de código C# neste tópico contém lógica de detecção e repetição personalizada para manipular erros transitórios.


O exemplo de código segue algumas diretrizes ou recomendações básicas que se aplicam independentemente da tecnologia usada para interagir com o Banco de Dados SQL Azure. Você pode ver as recomendações gerais em:


- [Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md)


O exemplo de código em C# consiste em um arquivo chamado Program.cs. Seu código é colado na próxima seção.


### Capturar e compilar o exemplo de código


Você pode compilar o exemplo com as seguintes etapas:


1. Na [edição gratuita do Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs), crie um novo projeto no modelo de Aplicativo do Console do C#.
 - Arquivo > Novo > Projeto > Instalado > Modelos > Visual C# > Windows > Área de Trabalho Clássica > Aplicativo de Console
 - Nomeie o projeto como **RetryAdo2**.

2. Abra o painel do Gerenciador de Soluções.
 - Veja o nome do seu projeto.
 - Veja o nome do arquivo Program.cs.

3. Salve o arquivo Program.cs.

4. Substitua por completo o conteúdo do arquivo Program.cs pelo código no bloco de código a seguir.

5. Clique no menu Compilar > Compilar Solução.


#### Código-fonte em C# para colar


Cole este código em seu arquivo **Program.cs**.


Em seguida, você deve editar as cadeias de caracteres de nome do servidor, senha e assim por diante. Você pode encontrar essas cadeias de caracteres no método chamado **GetSqlConnectionStringBuilder**.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			Program program = new Program();

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}

					program.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### Execute o programa


O executável **RetryAdo2.exe** não insere nenhum parâmetro. Para executar o .exe no Visual Studio:


1. Defina um ponto de interrupção na instrução **return;** no método **Main**.

2. Clique no botão de seta verde de início. Uma janela de console é exibida.

3. Quando o depurador parar ao final em **Main**, alterne para a janela do console.

4. Veja três linhas talvez idênticas às seguintes:


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## Testar sua lógica de repetição


Uma maneira útil de testar a lógica de repetição é:


1. Adicione temporariamente **11001** à sua coleção de valores **SqlConnection.Number** que devem ser considerados erros transitórios.

2. Recompile seu programa.

3. Desconecte o computador cliente da rede.

4. Execute o programa em um depurador, com um ponto de interrupção no loop.
 - O primeiro loop falhará com o erro 11001.

5. Quando o programa estiver em repouso em um ponto de interrupção durante o segundo loop, reconecte o computador à rede.

6. Retome a execução do programa. Ele obtém êxito durante o segundo loop.


### Outra opção de teste


Outra forma seria adicionar uma lógica ao seu programa para que ele reconheça um valor de parâmetro de linha de comando de “teste”. Em resposta ao parâmetro, o programa:


1. Acrescentaria temporariamente letras indesejadas para digitar incorretamente o nome do servidor do Banco de Dados SQL.

2. Adicionaria temporariamente **40615** à lista de erros transitórios.

3. No início de seu segundo loop, em outras palavras, seu primeiro loop de repetição, o programa:
 - Desfaria o erro ortográfico do servidor.
 - Removeria 40615 da lista transitória.


Execute o programa com o parâmetro “test” e verifique se ele primeiro falha, mas, em seguida, tem êxito em seu segundo loop.


## Links relacionados


- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO1-->