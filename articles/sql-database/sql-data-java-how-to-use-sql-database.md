<properties 
	pageTitle="Como usar o SQL Azure (Java) - guia de recursos do Azure" 
	description="Aprenda a usar o banco de dados SQL do Azure do código Java." 
	services="sql-database" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# Como usar o Banco de Dados SQL do Azure em Java

As etapas a seguir mostram como usar o Banco de Dados SQL do Azure com Java. Exemplos de linha de comando são mostrados para simplificar, mas etapas muito semelhantes podem ser apropriadas para aplicativos Web, independentemente de estarem hospedados no local, no Azure ou em outros ambientes. Este guia aborda a criação de um servidor e a criação de um banco de dados a partir do [Portal de Gerenciamento do Azure](https://windows.azure.com).

## O que é Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure fornece um sistema de gerenciamento de banco de dados relacional para o Azure e baseia-se na tecnologia do SQL Server. Com uma instância de Banco de Dados SQL, você pode provisionar e implantar facilmente soluções de banco de dados relacional na nuvem e usufruir de um data center distribuído que oferece disponibilidade, escalabilidade e segurança de classe empresarial com os benefícios da autorrecuperação e da proteção de dados interna.



## Conceitos
Como o Banco de Dados SQL do Azure tem como base as tecnologias do SQL Server, o acesso ao Banco de Dados SQL no Java é muito semelhante ao acesso ao SQL Server no Java. Você pode desenvolver um aplicativo localmente (usando o SQL Server) e depois conectar-se ao Banco de Dados SQL, alterando a cadeia de conexão. Você pode usar um driver JDBC do SQL Server para o seu aplicativo. No entanto, existem algumas diferenças entre o Banco de Dados SQL e o SQL Server que podem prejudicar seu aplicativo. Para obter mais informações, consulte [Diretrizes e limitações (Banco de Dados SQL) (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx)

Para obter recursos adicionais para o Banco de Dados SQL, consulte a seção [Próximas etapas][].

## Pré-requisitos

A seguir estão os pré-requisitos se você pretende usar o Banco de Dados SQL com Java.

* Um JDK (Java Developer Kit) versão 1.6 ou posterior.
* Uma assinatura de Azure, que pode ser adquirida em <http://www.microsoft.com/windowsazure/offers/>.
* Se estiver usando o Eclipse, você precisará do Eclipse IDE para desenvolvedores de Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>. Também precisará do plug-in do Azure para Eclipse com Java (da Microsoft Open Technologies). Durante a instalação desse plug-in, certifique-se de que o Microsoft JDBC Driver 4.0 para SQL Server esteja incluído. Para obter mais informações, consulte [Instalando o plug-in do Azure para Eclipse com Java (da Microsoft Open Technologies (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Se não estiver usando o Eclipse, você precisará do Microsoft JDBC Driver 4.0 para SQL Server, que pode ser baixado em <http://www.microsoft.com/download/details.aspx?id=11774>.

## Criando um Banco de Dados SQL do Azure

Antes de usar o Banco de Dados SQL do Azure no código Java, você deverá criar um servidor do Banco de Dados SQL do Azure.

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Novo**.

    ![Criar novo banco de dados SQL][create_new]

3. Clique em **Banco de Dados SQL** e, em seguida, clique em **Criação Personalizada**.

    ![Criar Banco de Dados SQL personalizado][create_new_sql_db]

4. Na caixa de diálogo **Configurações do banco de dados**, especifique o nome de seu banco de dados. Para as finalidades deste guia, use **gettingstarted** como o nome do banco de dados.
5. Para **Servidor**, selecione **Novo Servidor do Banco de Dados SQL**. Use os valores padrão para os outros campos.

    ![Configurações do Banco de Dados SQL][create_database_settings]

6. Clique na seta avançar.
7. Na caixa de diálogo **Configurações do servidor**, especifique um nome de logon do SQL Server. Para os fins deste guia, **MySQLAdmin** foi usado. Especifique e confirme uma senha. Especifique uma região e certifique-se de que a opção **Permitir que os serviços do Azure acessem o servidor** esteja marcada.

    ![Configurações do SQL Server][create_server_settings]

8. Clique no botão de conclusão.

## Determinando a cadeia de conexão do Banco de Dados SQL

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Banco de Dados SQL**.
3. Clique no banco de dados que você deseja usar.
4. Clique em **Mostrar cadeias de conexão**.
5. Realce o conteúdo da cadeia de conexão **JDBC**.

    ![Determinar a cadeia de conexão JDBC][get_jdbc_connection_string]

6. Clique com o botão direito do mouse no conteúdo realçado da cadeia de conexão **JDBC** e clique em **Copiar**.
7. Agora, você pode colar esse valor em seu arquivo de código para criar uma cadeia de conexão do formulário a seguir. Substitua *your_server* (em dois lugares) pelo texto que você copiou na etapa anterior e substitua *your_password* pelo valor da senha especificado quando você criou sua conta do Banco de Dados SQL. (Substitua também os valores atribuídos a **database=** e **user=** se você não tiver usado **gettingstarted** e **MySQLAdmin**, respectivamente.) 

    Cadeia connectionString = "jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" + "database=gettingstarted" + ";" + "user=MySQLAdmin@*your_server*" + ";" + "password=*your_password*" + ";" + "encrypt=true" + ";" + "hostNameInCertificate=*.int.mscds.com" + ";" + "loginTimeout=30";

Nós realmente usaremos essa cadeia de caracteres posteriormente neste guia. Por enquanto, você deve conhecer as etapas para determinar a cadeia de conexão. Além disso, dependendo das necessidades de seu aplicativo, você talvez não precise usar as configurações **encrypt** e **hostNameInCertificate** e talvez precise modificar a configuração **loginTimeout**.

## Para permitir o acesso a um intervalo de endereços IP

1. Logon no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Banco de Dados SQL**.
3. Clique em **Servidores**.
4. Clique no servidor que você deseja usar.
5. Clique em **Gerenciar**.
6. Clique em **Configurar**.
7. Em **Endereços IP permitidos**, digite o nome de uma nova regra de IP. Especifique o início e o final do intervalo de endereços IP. Para a sua conveniência, o endereço IP do cliente atual é exibido. O exemplo a seguir permite um único endereço IP de cliente (o seu endereço IP será diferente).

    ![Caixa de diálogo Endereços IP permitidos][allowed_ips_dialog]

8. Clique no botão de conclusão. Os endereços IP que você especificar agora terão acesso ao seu servidor de banco de dados.

## Para usar o Banco de Dados SQL do Azure em Java

1. Crie um projeto Java. Para os fins deste tutorial, chame-o de **HelloSQLAzure**.
2. Adicione um arquivo de classe Java chamado **HelloSQLAzure.java** ao projeto.
3. Adicione o **Driver JDBC da Microsoft para SQL Server** ao seu caminho de compilação.

   Se estiver usando o Eclipse:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/library/ms378526.aspx).

4. Em seu código **HelloSQLAzure.java**, adicione `import`instruções conforme mostrado a seguir:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Especifique sua cadeia de conexão. A seguir está um exemplo. Conforme mostrado acima, substitua *your_server* (em dois lugares), *your_user* e *your_password* pelos valores apropriados para seu servidor do Banco de Dados SQL.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

Agora, você está pronto para adicionar o código que irá se comunicar com o seu servidor do Banco de Dados SQL.

## Comunicando-se com o Banco de Dados SQL do Azure a partir de seu código

O restante deste tópico mostra exemplos que fazem o seguinte:

1. Conectar-se ao servidor do Banco de Dados SQL.
2. Definir uma instrução SQL, por exemplo, para criar ou remover uma tabela, inserir/selecionar/excluir linhas, etc.
3. Executar a instrução SQL, por meio de uma chamada para **executeUpdate** ou **executeQuery**.
4. Exibir resultados da consulta, se apropriado.

As seções a seguir devem ser lidas (amostradas) em ordem. O primeiro trecho é um exemplo completo; os outros podem depender de parte da estrutura do exemplo completo, como as instruções **import**, as declarações **class** e **main**, a manipulação de erros e o fechamento do recurso.

## Para criar uma tabela

O código a seguir mostra como criar uma tabela chamada **Person**.

	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class HelloSQLAzure {
	
	    public static void main(String[] args) 
	    {
	
			// Connection string for your SQL Database server.
			// Change the values assigned to your_server, 
			// your_user@your_server,
			// and your_password.
			String connectionString = 
				"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
					"database=gettingstarted" + ";" + 
					"user=your_user@your_server" + ";" +  
					"password=your_password";
			
			// The types for the following variables are
			// defined in the java.sql library.
			Connection connection = null;  // For making the connection
			Statement statement = null;    // For the SQL statement
			ResultSet resultSet = null;    // For the result set, if applicable
			
			try
			{
			    // Ensure the SQL Server driver class is available.
			    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			
			    // Establish the connection.
			    connection = DriverManager.getConnection(connectionString);
			
			    // Define the SQL string.
			    String sqlString = 
					"CREATE TABLE Person (" + 
			        	"[PersonID] [int] IDENTITY(1,1) NOT NULL," +
			            "[LastName] [nvarchar](50) NOT NULL," + 
			            "[FirstName] [nvarchar](50) NOT NULL)";
			
			    // Use the connection to create the SQL statement.
			    statement = connection.createStatement();
			
			    // Execute the statement.
			    statement.executeUpdate(sqlString);
			
			    // Provide a message when processing is complete.
			    System.out.println("Processing complete.");
			
			}
			// Exception handling
	        catch (ClassNotFoundException cnfe)  
	        {
	            
	            System.out.println("ClassNotFoundException " +
	                               cnfe.getMessage());
	        }
	        catch (Exception e)
	        {
	            System.out.println("Exception " + e.getMessage());
	            e.printStackTrace();
	        }
	        finally
	        {
	            try
	            {
	                // Close resources.
	                if (null != connection) connection.close();
	                if (null != statement) statement.close();
	                if (null != resultSet) resultSet.close();
	            }
	            catch (SQLException sqlException)
	            {
	                // No additional action if close() statements fail.
	            }
	        }
	        
	    }
	
	}
	

## Para criar um índice em uma tabela

O código a seguir mostra como criar um índice chamado **index1** na tabela **Person** usando a coluna **PersonID**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...



## Para inserir linhas

O código a seguir mostra como adicionar linhas à tabela **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"SET IDENTITY_INSERT Person ON " + 
	        	"INSERT INTO Person " + 
	            "(PersonID, LastName, FirstName) " + 
	            "VALUES(1, 'Abercrombie', 'Kim')," + 
	            	  "(2, 'Goeschl', 'Gerhard')," + 
	                  "(3, 'Grachev', 'Nikolay')," + 
	                  "(4, 'Yee', 'Tai')," + 
	                  "(5, 'Wilson', 'Jim')";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## Para recuperar linhas

O código a seguir mostra como recuperar linhas da tabela **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "SELECT TOP 10 * FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Loop through the results
	    while (resultSet.next())
	    {
	        // Print out the row data
	        System.out.println(
	        	"Person with ID " + 
	        	resultSet.getString("PersonID") + 
	        	" has name " +
	        	resultSet.getString("FirstName") + " " +
	       		resultSet.getString("LastName"));
	        }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 O código acima selecionou as dez principais linhas da tabela **Person**. Se você quiser retornar todas as linhas, modifique a instrução SQL para o seguinte:

	String sqlString = "SELECT * FROM Person";

 
## Para recuperar linhas usando uma cláusula WHERE

Para recuperar linhas usando uma cláusula, use o código conforme mostrado acima, exceto pela alteração na instrução SQL para incluir uma cláusula. A instrução SQL a seguir inclui uma cláusula para linhas cujo valor de **FirstName** é igual a **Jim**.

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
As cláusulas WHERE também pode ser usadas ao recuperar contagens, atualizar linhas ou excluir linhas.

## Para recuperar uma contagem de linhas

O código a seguir mostra como recuperar uma contagem de linhas da tabela **Person**.
 
	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	// Define the SQL string.
	    String sqlString = "SELECT COUNT (PersonID) FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Print out the returned number of rows.
	    while (resultSet.next())
	    {
	        System.out.println("There were " + 
	                         resultSet.getInt(1) +
	                         " rows returned.");
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Para atualizar linhas

O código a seguir mostra como atualizar linhas. Neste exemplo, o valor de **LastName** é alterado para **Kim** em todas as linhas em que o valor de **FirstName** é **Jim**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	    
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}// Exception handling and resource closing not shown...

 

## Para excluir linhas

O código a seguir mostra como excluir linhas. Neste exemplo, todas as linhas em que o valor de **FirstName** for igual a **Jim** serão excluídas.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DELETE from Person " + 
				"WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...
	
 
## Para verificar se existe uma tabela

O código a seguir mostra como determinar se existe uma tabela.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"IF EXISTS (SELECT 1 " +
	        	"FROM sysobjects " + 
	            "WHERE xtype='u' AND name='Person') " +
	            "SELECT 'Person table exists.'" +
	            "ELSE  " +
	            "SELECT 'Person table does not exist.'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Display the result.
	    while (resultSet.next())
	    {
	        System.out.println(resultSet.getString(1));
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Para remover um índice

O código a seguir mostra como remover um índice chamado **index1** da tabela **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
			"database=gettingstarted" + ";" +
			"user=your_user@your_server" + ";" +
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DROP INDEX index1 " + 
	        	"ON Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## Para remover uma tabela

O código a seguir mostra como remover uma tabela chamada **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "DROP TABLE Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Usando o Banco de Dados SQL em Java em uma implantação do Azure

Para usar o Banco de Dados SQL em Java em uma implantação do Azure, além de ter o Microsoft JDBC Driver 4.0 para SQL Server como uma biblioteca em seu caminho de classe, como mostrado acima, você deverá empacotá-lo com sua implantação.


**Empacotando o Microsoft JDBC Driver 4.0 para SQL Server se estiver usando o Eclipse**

1. Dentro do explorador de projetos do Eclipse, clique com o botão direito do mouse no projeto e clique em **Propriedades**.
2. No painel esquerdo da caixa de diálogo **Propriedades**, clique em **Assembly de Implantação**e, em seguida, clique em **Adicionar**.
3. Na caixa de diálogo **Nova Diretiva de Assembly**, clique em **Entradas do Caminho de Compilação Java** e, em seguida, clique em **Avançar**.
4. Selecione **Microsoft JDBC Driver 4.0 para SQL Server** e clique em **Concluir**.
5. Clique em **OK** para fechar a caixa de diálogo **Propriedades**.
6. Exporte o arquivo WAR de seu projeto para a pasta approot e recompile seu projeto do Azure, de acordo com as etapas documentadas em [Criando um aplicativo Hello World usando o plug-in do Azure para Eclipse com Java (da Microsoft Open Technologies) (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). Este tópico também descreve como executar seu aplicativo no emulador de computação e no Azure.

**Empacotando o Microsoft JDBC Driver 4.0 para SQL Server se não estiver usando o Eclipse**

* Certifique-se de que a biblioteca do Microsoft JDBC Driver 4.0 para SQL Server esteja incluída na mesma função do Azure que seu aplicativo Java, bem como adicionada ao caminho de classe de seu aplicativo.

## Próximas etapas

Para saber mais sobre o Microsoft JDBC Driver para SQL Server, consulte [Visão geral do JDBC Driver](http://msdn.microsoft.com/library/ms378749.aspx). Para saber mais sobre o Banco de Dados SQL, consulte [Visão geral do Banco de Dados SQL (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx).

[Concepts]: #concepts
[Prerequisites]: #prerequisites
[Creating an Azure SQL Database]: #create_db
[Determining the SQL Database connection string]: #determine_connection_string
[To allow access to a range of IP addresses]: #specify_allowed_ips
[To use Azure SQL Database in Java]: #use_sql_azure_in_java
[Communicating with Azure SQL Database from your code]: #communicate_from_code
[To create a table]: #to_create_table
[To create an index on a table]: #to_create_index
[To insert rows]: #to_insert_rows
[To retrieve rows]: #to_retrieve_rows
[To retrieve rows using a WHERE clause]: #to_retrieve_rows_using_where
[To retrieve a count of rows]: #to_retrieve_row_count
[To update rows]: #to_update_rows
[To delete rows]: #to_delete_rows
[To check whether a table exists]: #to_check_table_existence
[To drop an index]: #to_drop_index
[To drop a table]: #to_drop_table
[Using SQL Database in Java within an Azure Deployment]: #using_in_azure
[Próximas etapas]: #nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
 

<!---HONumber=62-->