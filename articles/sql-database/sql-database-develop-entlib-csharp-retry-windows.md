<properties 
	pageTitle="Tentativa da EntLib de se conectar ao Banco de Dados SQL | Microsoft Azure"
	description="A Enterprise Library foi projetada para facilitar várias tarefas para os programas clientes de serviços de nuvem, incluindo a integração da lógica de repetição para falhas transitórias."
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
	ms.date="10/16/2015" 
	ms.author="genemi"/>


# Exemplo de código: lógica de repetição da Enterprise Library 6, em C&#x23; para conexão com o Banco de Dados SQL


Este tópico apresenta um exemplo de código completo que demonstra a Enterprise Library (EntLib). A EntLib simplifica muitas tarefas para programas clientes que interagem com os serviços de nuvem, como o Banco de Dados SQL do Azure. Nosso exemplo se concentra na importante tarefa de incluir a lógica de repetição para falhas transitórias.


As classes da EntLib foram projetadas para distinguir duas categorias de erros de tempo de execução:

- Erros que nunca serão corrigidos automaticamente, como um nome de servidor escrito incorretamente.
- Falhas transitórias, como a suspensão por parte do servidor da aceitação de novas conexões durante vários segundos, enquanto o sistema do Azure equilibra a carga.

A Enterprise Library 6 (EntLib60) é a versão mais recente e foi lançada em abril de 2013.


## Pré-requisitos


#### .NET Framework 4.0 ou superior.


Microsoft .NET Framework 4.0 ou superior deve estar instalado. Até o momento de produção deste artigo, a versão 4.6 estava disponível e recomendamos a versão mais recente.


#### A edição do Visual Studio Community é gratuita


Você precisa de uma forma de compilar o código-fonte deste exemplo. Uma maneira é instalar a [edição gratuita do Microsoft Visual Studio *Community*](http://www.visualstudio.com/products/free-developer-offers-vs.aspx).


Talvez seja necessário registrar seu endereço de email no MSDN. As etapas são parecidas com a seguinte:


1. [Acesse o MSDN](http://msdn.microsoft.com/).
2. Clique em **Assinaturas do MSDN** na parte superior.
3. Clique em **Inscreva-se agora**.
4. Preencha o formulário com suas informações.
5. Clique em **Criar uma conta** na parte inferior.


#### Enterprise Library 6 (EntLib60)


Maneiras de instalar a EntLib60:


- Use o recurso gerenciador de pacotes *NuGet* no Visual Studio:
 - No NuGet, procure **enterpriselibrary**.


- No [tópico de documentação inicial para a EntLib60](http://msdn.microsoft.com/library/dn169621.aspx), encontre a linha **Downloads** e clique em [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898) para baixar os arquivos binários de assembly .DLL.


A EntLib60 tem vários arquivos de assembly .DLL cujos nomes começam com o mesmo prefixo **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**, mas este exemplo de código só está interessado nestes dois assemblies:

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## Como as classes EntLib se complementam


As classes EntLib são usadas para criar outras classes EntLib. Neste exemplo de código, a sequência de construção e de uso ocorre como listado a seguir:


1. Criar um objeto **ExponentialBackoff**.
2. Criar um objeto **SqlDatabaseTransientErrorDetectionStrategy**.
3. Criar um objeto **RetryPolicy**. Os parâmetros de entrada são:
 - O objeto **ExponentialBackoff**.
 - O objeto **SqlDatabaseTransientErrorDetectionStrategy**.
4. Construir um objeto **ReliableSqlConnection**. Os parâmetros de entrada são:
 - Um objeto **String**, com o nome do servidor e outras informações de conexão.
 - Objeto **RetryPolicy**.
5. A chamada para conexão, feita por meio do método **RetryPolicy .ExecuteAction**.
6. Chamar o método **ReliableSqlConnection .CreateCommand**.
 - Retorna um objeto **System.SqlClient.Data.DbCommand**, parte do ADO.NET.
7. A chamada para consulta, feita por meio do método **RetryPolicy .ExecuteAction**.


## Compilar e executar o exemplo de código


O exemplo de código-fonte Program.cs será fornecido posteriormente neste tópico. Você pode compilar e executar o exemplo com as seguintes etapas:


1. No Visual Studio, crie um novo projeto por meio do modelo de Aplicativo de Console em C#.

2. No painel do Gerenciador de Soluções, edite o arquivo praticamente vazio Program.cs, substituindo seu conteúdo inicial pelo código de Program.cs fornecido mais adiante neste tópico.

3. Use o menu Compilação > Compilar Solução no Visual Studio para compilar o projeto.

4. Em uma janela de comando cmd.exe, execute o programa conforme mostrado a seguir. Também será mostrada a saída real de uma execução:


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Código-fonte de Program.cs


Todo código-fonte para este exemplo da EntLib está contido no seguinte arquivo Program.cs.


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## Links relacionados


- Consulte vários links para outras informações em: [Enterprise Library 6 – abril de 2013](http://msdn.microsoft.com/library/dn169621.aspx)
 - Este tópico tem um botão na parte superior que oferece o [download do código-fonte da EntLib60](http://go.microsoft.com/fwlink/p/?LinkID=290898), se você estiver curioso para ver o código.


- Livro eletrônico gratuito no formato .PDF da Microsoft: [Guia do desenvolvedor para a Microsoft Enterprise Library, 2ª edição](http://www.microsoft.com/download/details.aspx?id=41145).


- [Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling Namespace](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Referência à Biblioteca de Classes da Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)


- [Exemplo de código: lógica de repetição em C# para se conectar ao Banco de Dados SQL com ADO.NET](sql-database-develop-csharp-retry-windows.md)


- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO4-->