<properties 
	pageTitle="Exemplo de código: lógica de repetição da Enterprise Library, em C# para se conectar ao Banco de Dados SQL | Microsoft Azure"
	description="A Enterprise Library foi projetada para facilitar a tarefa de inclusão da lógica de repetição para seus programas de cliente que acessam os serviços de nuvem."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# Exemplo de código: lógica de repetição da Enterprise Library, em C&\#x23; para se conectar ao Banco de Dados SQL


Este tópico apresenta um exemplo de código completo que demonstra a Enterprise Library (EntLib). A EntLib facilita a tarefa do programador de incluir a lógica de repetição em seu cliente que interage com o serviço de nuvem do Banco de Dados SQL do Microsoft Azure.


A Enterprise Library 6 (EntLib60) é a versão mais recente.


Obtenha informações mais detalhadas nestes links recomendados sobre a EntLib:


- [Enterprise Library 6 – abril de 2013](http://msdn.microsoft.com/library/dn169621.aspx)<br/>Fornece diversos links para mais informações.

- Livro eletrônico gratuito no formato .PDF da Microsoft:<br/>[Guia do desenvolvedor para o Microsoft Enterprise Library, 2nd Edition](http://www.microsoft.com/download/details.aspx?id=41145).


## Pré-requisitos


A EntLib não está incluída quando você instala o Visual Studio e o Microsoft .NET Framework. Você deve executar uma ação de download separada.


O sistema **NuGet** por meio do Visual Studio facilita o download. O download resulta em um diretório chamado packages\\ no mesmo diretório no qual o arquivo .sln da solução Visual Studio está armazenado.


Os arquivos .dll do assembly da EntLib são instalados em um subdiretório. Os dois arquivos de assembly são nomeados da seguinte maneira:


- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.dll`
- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.Data.dll`


## Arquivos de código C&\#x23; explicados


O exemplo de código C# é composto por três arquivos .cs cujo conteúdo é colado nas seções a seguir. Seus nomes de arquivo são:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`
- `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


### `Program.cs` mais curto sem `try/catch`


Outro arquivo .cs fornecido é uma versão reduzida de `Program.cs`. Todo o código `try/catch` foi removido. Isso facilita a observação das chamadas da EntLib.



### Testar o arquivo .cs


É complicado testar sua lógica de repetição, pois não há uma maneira óbvia de causar um erro de falha transitória. Uma solução de teste é usar um código temporário para:


1. Causar uma falha transitória falsa.
2. Corrigir a causa da falha transitória.
3. Testar novamente a conexão ou a consulta, esperando o sucesso.


Para ajudar você com o teste, outro arquivo .cs é fornecido como um substituto temporário:


- `Test2_TransientErrorDetectionStrategy`
 - Substituição temporária para o `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`.


Os locais do código para fazer as alterações relacionadas a este teste estão marcados com um comentário que contém a cadeia de caracteres `TEST.PASSWORD_FIX`.


## Compilar e executar o exemplo de código


Você pode compilar o exemplo com as seguintes etapas:


1. No Visual Studio, crie um novo projeto por meio do modelo de Aplicativo de Console em C#.

2. Clique com o botão direito do mouse em seu projeto e adicione os arquivos .cs para os quais o código-fonte foi fornecido neste tópico.

3. Em uma janela de comando `cmd.exe`, execute o programa como mostrado a seguir. Também será mostrada a saída real de uma execução:


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


O código-fonte C# para os arquivos .cs está nas seções a seguir.


## Arquivo Program.cs


O seguinte arquivo de código, Program.cs, é longo porque contém blocos `try/catch` executados somente no caso de um erro. Próximo ao final do tópico atual há uma versão muito menor de `Program.cs` com todas as linhas `try/catch` removidas.


O método `Main` está em `Program.cs`. A pilha de chamadas é executada da seguinte maneira:


1. `Main` chama `ConnectAndQuery`.

2. `ConnectAndQuery` chama `InitializeFields`.

3. `ConnectAndQuery` chama `EstablishConnection`.

4. `EstablishConnection` chama `IssueQueryCommand`.


&nbsp;


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1 
	{
	    /// <summary>
	    /// Demo sample C# program that uses Microsoft's Enterprise Library 6 to robustly
	    /// connect to and interact with Azure SQL Database.
	    /// .
	    /// The sample chooses a strategy of discarding the Sql Connection whenever
	    /// a Sql Command fails, even for a transient reason. The 'for' loop
	    /// manages this choice. We are neither recommending nor disrecommending
	    /// this particular choice, just demonstrating flexibility.
	    /// .
	    /// For an optional test mechanism manually built into this program, find all:
	    /// 'TEST.PASSWORD_FIX' (related to connection).
	    /// </summary>
	    class Program
	    {
	        // const fields, so that SqlException objects thrown from Sql Commands
	        // are tested for transience by our custom logic.
	        private const string M_progressCreatingConnection = "CreatingConnection";
	        private const string M_progressIssuingCommand     = "IssuingCommand";
	
	        // Fields, shared among methods.
	        private string progressLocation;
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        // Also consider E.FixedInterval or E.Incremental.
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	
	        // TEST.PASSWORD_FIX.  Replace with Test2_TransientErrorDetectionStrategy.
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	
	        /// <summary>
	        /// Called from Main.
	        /// Calls a method to establish a connection, which calls
	        /// another method to issue a query through the connection. 
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int maxCountTriesConnectAndQuery = 3;  // Adjustable.
	
	            this.InitializeFields();
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	            // Next is a call .ExecuteAction to invoke the method EstablishConnection.
	            // Method EstablishConnection then calls another .ExecuteAction to
	            //     invoke the method IssueQueryCommand.
	            // Then control is returned to this method.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                if (cc >= 2) { Console.WriteLine("-- Another iteration of outer loop, cc=={0}. --", cc); }
	
	                try
	                {
	                    // [A.1] Connect, which proceeds to issue a query command.
	                    this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	
	                    break; // [A.2] All has gone well, so let the program end.
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientErrorNumber = false;
	
	                    // [A.3] Call our collection of transient error numbers.
	                    isTransientErrorNumber =
	                        Custom_SqlDatabaseTransientErrorDetectionStrategy // TEST.PASSWORD_FIX.
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientErrorNumber == false)
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number = {0}."
	                            + "  Will terminate.", sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.4] Either the connection attempt or the query command attempt
	                        //     suffered a persistent SqlException.
	                        // Break the query command loop, let the hopeless program end.
	                        break;
	                    }
	
	                    if (this.progressLocation == M_progressCreatingConnection)
	                    {
	                        // [A.5] The EntLib retry mechanisms already gave the Sql Connection
	                        //     enough retries, so this block will not give connection any more tries.
	                        // This block gives retries to certain Sql Command only.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient error from an attempt
	                    //     to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new
	                    // query attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number = {0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught.  Will terminate.");
	                    throw exc; // [A.7] The program must end, so re-throw the unrecognized exception.
	                }
	
	
	                // [A.8] Throw an exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        void EstablishConnection()
	        {
	            try
	            {
	                // [C.1]
	                using ( this.rsConnection = new E.ReliableSqlConnection(
	                    this.scsBuilder.ToString(),
	                    this.connectionRetryPolicy,
	                    this.commandRetryPolicy )
	                    )
	                {
	                    this.progressLocation = M_progressCreatingConnection;
	                    this.rsConnection.Open(); // [C.2] Open the newly constructed reliable connection.
	
	                    // [C.3] Prepare delegate, then issue a query command.
	                    this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	                }
	            }
	            // TEST.PASSWORD_FIX.
	                // Step_1: In InitializeFields(), set password to a wrong value.
	                // Step_2: Uncomment the following 'catch' of sqlExc.
	            //catch (C.SqlException sqlExc)  // For TESTING only.  Usually comment out this one CATCH.
	            //{
	            //    if (sqlExc.Number == 18456)  // Could mean wrong password, could not connect.
	            //    {
	            //        this.scsBuilder["Password"] = "MyCorrectPassword";
	            //        Console.WriteLine("Testing.  Password now fixed in catch.");
	            //    }
	            //    throw sqlExc;
	            //}
	            catch (Exception exc)
	            {
	                throw exc;  // [C.4] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            try
	            {
	                // [D.1] Use the connection to create a query command.
	                using (dbCommand = this.rsConnection.CreateCommand())
	                {
	                    dbCommand.CommandText = this.sqlSelectCode;
	
	                    // [D.2] Issue the query command through the connection.
	                    this.progressLocation = M_progressIssuingCommand;
	                    using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                    {
	                        Console.WriteLine();
	                        // [D.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // [D.4] Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	
	
	        void InitializeFields()
	        {
	            // [B.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyCorrectPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30; // Default is 15 seconds, too brief over Internet!
	
	            // [B.2] Prepare the Transact-SQL select statement.
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            // [B.3] Begin steps to create a retry policy for connecting.
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,                               // Maximum number of times to retry.
	                TimeSpan.FromMilliseconds(2000), // Minimum interval between retries.
	                TimeSpan.FromMilliseconds(8000), // Maximum interval between retries.
	                TimeSpan.FromMilliseconds(2000)  // Factor for random differences in interval between retries.
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();// TEST.PASSWORD_FIX.
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	
	            return;
	        } // method InitializeFields
	    } // class Program
	}


&nbsp;


## Arquivo `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


A EntLib60 tem uma classe chamada `ReliableSqlConnection`. Você pode controlar como a instância de conexão decide se uma exceção é um erro transitório atribuindo a ela uma classe que implementa `ITransientErrorDetectionStrategy`.

A EntLib60 oferece a classe `SqlDatabaseTransientErrorDetectionStrategy`. Mas, neste tópico, optamos por implementar e usar nossa própria classe personalizada `Custom_SqlDatabaseTransientErrorDetectionStrategy`. Nossa classe personalizada tem uma lista de valores válidos para comparação com `SqlException.Number`.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method is required by ITransientErrorDetectionStrategy.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613};
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


&nbsp;


## Arquivo `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


Essa classe personalizada que implementa `ITransientErrorDetectionStrategy` impõe nossa opção de nunca permitir que a EntLib60 considere qualquer erro de comando de consulta como um simples erro transitório. Em vez disso, o programa geral é projetado para avaliar a `SqlException.Number` em sua própria lógica personalizada.


Com a classe nesta seção, fizemos a escolha de design para que nosso programa descarte e recrie uma nova conexão antes de tentar novamente qualquer comando de consulta com falha, transitória ou não.


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// Is a substitute for class E.SqlCommandTransientErrorDetectionStrategy.
	    /// Choosing to always discard the Sql Connection whenever the command fails,
	    /// regardless of whether command failure was transient.
	    /// Program try/catch and for-loop logic prefers to discard then re-construct
	    /// a new Sql Connection.
	    /// </summary>
	    public class ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            return false;  // Force a failure, even if is transient.  No retry of sql command.
	        }
	    }
	}


&nbsp;


## Testar sua lógica de repetição


É difícil testar seu programa da EntLib60. É difícil causar um erro transitório real e controlar seus intervalos. Esse código de demonstração inclui uma técnica simples que você pode usar para simular um erro transitório.


A técnica é usar nossa classe detectora personalizada `Test2_TransientErrorDetectionStrategy` em vez da classe `Custom_SqlTransientErrorDetectionStrategy`. Dessa forma, você pode forçar a `ReliableSqlConnection` da EntLib60 a avaliar *todas* as ocorrências de `SqlException` como transitórias. O truque é fazer com que o programa corrija automaticamente o problema antes da primeira tentativa.


### Truque da senha digitada incorretamente


Uma abordagem simples é começar com um valor digitado incorretamente para a senha de conexão. Quando isso causa uma `SqlException`, com o `sqlExc.Number==18456`, o programa deve corrigir a senha. Na repetição, o programa é bem-sucedido.


Para implementar esse teste em nosso programa de demonstração, pesquise o código-fonte de todas as ocorrências de `TEST.PASSWORD_FIX`. Depois de localizar todas as ocorrências de `TEST.PASSWORD_FIX` e fazer as alterações explicadas nos comentários em cada local, você terá feito as seguintes alterações:


- No código ativo no método `InitializeFields`, altere o valor da senha para um valor incorreto.

- No código comentado `//`, no método `EstablishConnection`, há todo um bloco `catch`...<br/>`//catch (SDSqlC.SqlException sqlExc)`<br/> comentado. Remova o comentário de todo o bloco.

- No novo bloco `catch` sem comentários, atribua o valor de senha correto.

- Quando houver uma referência à classe `Custom_TransientErrorDetectionStrategy` no código, substitua a referência por `Test2_TransientErrorDetectionStrategy`. Você pode manter o mesmo nome da variável.


### Arquivo `Test2_TransientErrorDetectionStrategy.cs`


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// For testing, is a substitute for class E.SqlDatabaseTransientErrorDetectionStrategy.
	    /// Change the true/false assignment to returnValue for different tests.
	    /// </summary>
	    public class Test2_TransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            bool returnValue = Test2_TransientErrorDetectionStrategy.IsTransientStatic(exc);
	            Console.WriteLine("Inside Test2_TransientErrorDetectionStrategy .IsTransientStatic,"
	                + " returning {0}.", returnValue);
	            return returnValue;
	        }
	        static public bool IsTransientStatic(Exception exc)
	        {
	            return true;  //false;
	        }
	    }
	}


&nbsp;


## `Program.cs` mais curto, sem `try/catch`


O código mais curto nesta seção é compilado e, quando executado, normalmente funciona. Mas o código mais curto não deve ser executado na produção.


A única finalidade deste exemplo de `Program.cs` mais curto é facilitar a observação das chamadas EntLib no `Program.cs` real. A remoção de todos os blocos `try/catch` facilita a observação das chamadas EntLib.


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1_Shorter_No_TryCatch
	{
	    class Program
	    {
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.InitializeFields();
	            this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using ( this.rsConnection = new E.ReliableSqlConnection(
	                this.scsBuilder.ToString(),
	                this.connectionRetryPolicy,
	                this.commandRetryPolicy )
	                    )
	            {
	                this.rsConnection.Open();
	                this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	            }
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            using (dbCommand = this.rsConnection.CreateCommand())
	            {
	                dbCommand.CommandText = this.sqlSelectCode;
	                using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	
	        void InitializeFields()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,
	                TimeSpan.FromMilliseconds(2000),
	                TimeSpan.FromMilliseconds(8000),
	                TimeSpan.FromMilliseconds(2000)
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	        } // method InitializeFields
	    } // class Program
	}


## Links relacionados


- [Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling Namespace](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)

- [Biblioteca de classes do Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)

- [Exemplo de código: lógica de repetição em C# para se conectar ao Banco de Dados SQL com ADO.NET](sql-database-develop-csharp-retry-windows.md)

- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=06-->