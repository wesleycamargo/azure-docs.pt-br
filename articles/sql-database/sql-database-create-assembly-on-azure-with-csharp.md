<properties 
	pageTitle="CREATE ASSEMBLY no banco de dados SQL do Azure com CSharp"
	description="Fornece código-fonte C# para emitir CREATE ASSEMBLY para o Banco de Dados SQL do Azure após a primeira codificação de um arquivo DLL em uma cadeia de caracteres que contém um número hexadecimal longo." 
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
	ms.date="04/17/2015" 
	ms.author="genemi"/>


# CREATE ASSEMBLY no banco de dados SQL do Azure com CSharp


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


Este tópico fornece um exemplo de código C# que você pode usar para emitir uma instrução [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) para o Banco de Dados SQL do Azure. Para o banco de dados SQL, a cláusula FROM não pode aceitar o formato simples de um caminho no computador local que hospeda o banco de dados. Uma alternativa é primeiro codificar os bits binários da DLL do assembly em uma cadeia de caracteres longa que contém um número hexadecimal. Em seguida, forneça a cadeia de caracteres como o valor na cláusula FROM.


### Pré-requisitos


Para entender esse tópico, você já deve saber parcialmente o seguinte:


- [Funções com valor de tabela CLR](http://msdn.microsoft.com/library/ms131103.aspx)<br/>Explica como a instrução Transact-SQL CREATE ASSEMBLY funciona com outras instruções para o Microsoft SQL Server local.


## R. Técnica geral


1. DROP FUNCTION e DROP ASSEMBLY, se necessário, para limpar uma execução anterior.
2. Lembre-se da localização do arquivo DLL assembly do Microsoft .NET Framework que você compilou por meio do seu próprio código. Forneça a localização na próxima etapa. 
3. Execute o EXE para o qual o código-fonte C# é fornecido neste tópico. Informe o EXE onde está o arquivo DLL.
 - Codifica sua DLL binária em uma cadeia de caracteres longa que contém um número hexadecimal.
 - Emite uma instrução CREATE ASSEMBLY com a cadeia de caracteres hexadecimal fornecida na cláusula FROM.
4. [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx) para fazer referência a um método em seu assembly.
5. Instrução T-SQL SELECT para chamar e testar sua função.


A lista acima não menciona...<br/> **execute sp_configure 'clr enabled', 1;**<br/> ...pois não é necessário para o Banco de Dados SQL do Azure, mesmo que seja necessário para o Microsoft SQL Server.


Se for necessário para repetir a execução, o código T-SQL para descartar a função e o assembly é o seguinte:


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. DLL de assembly simples da função T-SQL para referência


O exemplo de código C# trivial nesta seção pode ser compilado em um arquivo de DLL do assembly.


Esse exemplo de código contém o método **CompareCaseSensitiveNet** que é referenciado posteriormente em uma instrução T-SQL CREATE FUNCTION. Observe que o método é decorado com um atributo .NET chamado **SqlFunction**. Um método que é decorado com esse atributo pode ser chamado por seu T-SQL como uma função.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. C&#x23; o exemplo de código para EXE que emite CREATE ASSEMBLY


A seguinte sequência ocorre ao executar o EXE que é criado por meio deste exemplo C#:


1. A execução da linha de comando de EXE chama o método **Main**.
2. Main chama o método **ObtainHexStringOfAssembly**.
 - O método gera uma SqlString que armazena o assembly como um número hexadecimal.
3. Main chama o método **SubmitCreateAssemblyToAzureSqlDb**.
 - A entrada principal é o SqlString.
 - A saída é uma chamada de CREATE ASSEMBLY enviada ao banco de dados SQL do Azure.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 Versões e referências de compilação


Quando o código de amostra é compilado e testado para a ferramenta EXE, usamos o seguinte:


- Visual Studio 2013, atualização 4
 - Nosso tipo de modelo de projeto era o aplicativo de console simples.
- .NET Framework 4.5


Nosso projeto do Visual Studio referenciado para compilar os assemblies a seguir:


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 Linha de comando para executar o EXE


O bloco de código a seguir mostra um exemplo da linha de comando que você insere para executar o arquivo EXE no console. Os parâmetros na linha de comando são artificialmente envolvidos aqui para melhor exibição.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


Para manter a simplicidade da explicação, este exemplo passa a senha como um parâmetro de linha de comando. Um design melhor é fazer com que o código C# obtenha a senha por meio de um arquivo CONFIG.


## D. Executar uma instrução CREATE FUNCTION


Depois que o assembly é armazenado em seu servidor de banco de dados SQL Azure, você deve executar uma instrução Transact-SQL CREATE FUNCTION que faz referência ao método no assembly.


O seguinte bloco de código Transact-SQL inclui duas instruções SELECT não essenciais para mostrar a prova de que o sistema de banco de dados tem registros para o assembly e sua função. Por fim, existe uma SELECT que chama a função.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


O bloco de código Transact-SQL anterior termina com uma instrução SELECT que chama a nova função. Você pode colocar a instrução SELECT em um procedimento armazenado.


<!-- EndOfFile -->

<!---HONumber=July15_HO2-->