<properties
	pageTitle="Lógica de repetição em C#, para o Banco de Dados SQL | Microsoft Azure"
	description="O exemplo em C# inclui uma lógica de repetição para interagir de forma confiável com o Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="annemill"/>


# Exemplo de código: repetir a lógica em C# para se conectar ao Banco de Dados SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



Este tópico fornece um exemplo de código C# que demonstra uma lógica de repetição personalizada. A lógica de repetição foi criada para processar normalmente erros transitórios ou *falhas transitórias* que tendem a desaparecer caso o programa aguarde vários segundos e tente novamente.


As classes ADO.NET para se conectar ao Microsoft SQL Server local também podem se conectar ao Banco de Dados SQL do Azure. No entanto, por si só, as classes ADO.NET não podem fornecer toda a solidez e confiabilidade necessárias para o uso na produção. O programa cliente pode encontrar falhas transitórias das quais ele deve se recuperar silenciosamente e continuar por conta própria.


Entre alguns exemplos de falhas transitórias estão:


- Uma conexão pela Internet está sujeita a breves interrupções de rede, após as quais a conexão pode ser recriada.
- A computação em nuvem envolve o balanceamento de carga, que pode rapidamente bloquear tentativas de conexão ou de consulta.


## R. Identificar erros transitórios


O programa deve distinguir entre erros transitórios versus erros persistentes. Se o programa tiver um erro de ortografia no nome do banco de dados de destino, o erro "Nenhum banco de dados encontrado" persistirá e ocorrerá novamente sempre que você executar novamente o programa.

A lista com os números dos erros que são categorizados como falhas transitórias está disponível em:

- [Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - Veja a seção superior sobre *Erros transitórios e erros de perda de conexão*.


O exemplo de código C# neste tópico lista os números de erro transitório em um campo chamado **TransientErrorNumbers**.



## B. Exemplo de código C#


O exemplo de código C# neste tópico contém lógica de detecção e repetição personalizada para manipular erros transitórios. O exemplo pressupõe que o .NET Framework 4.5.1 ou posterior esteja instalado.


O exemplo de código segue algumas diretrizes ou recomendações básicas que se aplicam independentemente da tecnologia usada para interagir com o Banco de Dados SQL Azure. Você pode ver as recomendações gerais em:


- [Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md)


O exemplo de código em C# consiste em um arquivo chamado Program.cs. Seu código é fornecido na próxima seção.


### B.1 Capturar e compilar o exemplo de código


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
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C. Execute o programa


O executável **RetryAdo2.exe** não insere nenhum parâmetro. Para executar o .exe:

1. Abra uma janela de console para onde você tiver compilado o binário RetryAdo2.exe.
2. Execute o RetryAdo2.exe, sem nenhum parâmetro de entrada.



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D. Maneiras de testar sua lógica de repetição

Há diversas maneiras que você pode simular um erro transitório para testar sua lógica de repetição.


### D. 1 Lançar uma exceção de teste

O exemplo de código inclui:

- Uma pequena segunda classe chamada **TestSqlException**, que é uma propriedade chamada **Número**.
- `//throw new TestSqlException(4060);`, do qual você pode remover a marca de comentário.

Se você remover a marca comentário do lançamento e recompilar, a próxima execução do **RetryAdo2.exe** gerará algo semelhante ao seguinte.



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### Teste novamente com um erro persistente


Para provar que o código lida com erros persistentes corretamente, execute novamente o teste anterior, mas não use o número de um erro transitório real como 4060. Em vez disso, use o número sem sentido 7654321. O programa deve tratar isso como um erro persistente e deve ignorar qualquer nova tentativa.



### D.2 Desconectar da rede

1. Desconecte o computador cliente da rede.
  - Para uma área de trabalho, desconecte o cabo de rede.
  - Para um laptop, pressione a combinação de função das teclas para desativar o adaptador de rede.
2. Inicie o RetryAdo2.exe e aguarde o console exibir o primeiro erro transitório, provavelmente 11001.
3. Reconecte-se à rede, enquanto o RetryAdo2.exe continua em execução.
4. Observe o console relatar o sucesso em uma nova tentativa subsequente.


### D.3 Temporariamente digitar incorretamente o nome do servidor

1. Adicione temporariamente 40615 como outro número de erro para **TransientErrorNumbers** e recompile.
2. Defina um ponto de interrupção na linha: `new S.SqlConnectionStringBuilder()`.
3. Use o recurso *Editar e Continuar* para propositalmente digitar o nome do servidor de maneira incorreta algumas linhas abaixo.
  - Deixe o programa ser executado e voltar ao ponto de interrupção.
  - O erro 40615 ocorre.
4. Corrija o erro de ortografia.
5. Deixe o programa ser executado e concluir com êxito.
6. Remova o 40615 e recompile.


## E. Links relacionados

- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Experimentar o Banco de Dados SQL: Usar o C# para criar um Banco de Dados SQL com a Biblioteca do Banco de Dados SQL para .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->