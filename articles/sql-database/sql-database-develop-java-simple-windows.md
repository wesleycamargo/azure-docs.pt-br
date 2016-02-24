<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows"
	description="Apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo usa o JDBC, e é executado em um computador cliente com Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="lbosq"/>


# Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows


[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo de Java se baseia no Java Development Kit (JDK) versão 1.8. O exemplo se conecta a um Banco de Dados SQL do Azure usando o driver JDBC.


## Pré-requisitos

### Drivers e bibliotecas

- [Driver do Microsoft JDBC para SQL Server - SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- Qualquer plataforma de sistema operacional que execute o [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

### Um Banco de Dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados.

### Uma tabela SQL

O exemplo de código Java deste tópico pressupõe que a tabela de teste a seguir já exista no Banco de Dados SQL do Azure.

<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## Etapa 1: Obter a cadeia de conexão

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE]Se você estiver usando o driver JTDS JDBC, você precisará adicionar "ssl=require" à URL da cadeia de conexão e precisará também definir a seguinte opção para o JVM: "-Djsse.enableCBCProtection=false". Essa opção do JVM desabilita uma correção para uma vulnerabilidade de segurança, portanto certifique-se de entender o risco envolvido antes de configurá-la.


## Etapa 2: Compilar o exemplo de código Java


A seção contém grande parte do exemplo de código Java. Ela contém comentários indicando onde você poderia copiar e colar os segmentos Java menores apresentados nas seções subsequentes. O exemplo nesta seção poderia ser compilado e executado mesmo sem as ações de copiar e colar nos comentários, mas ele apenas se conectaria e, em seguida, seria encerrado. Estes são os comentários que você encontrará:


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


Veja a seguir grande parte do exemplo de código Java. O exemplo inclui a função `main` da classe `SQLDatabaseTest`.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;

	public class SQLDatabaseTest {

		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;"
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password=your_password;"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;";

			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;

			try {
				connection = DriverManager.getConnection(connectionString);

				// INSERT two rows into the table.
				// ...

				// TRANSACTION and commit for an UPDATE.
				// ...

				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


Para realmente executar o exemplo de código Java anterior, seria necessário colocar os valores reais na cadeia de conexão a fim de substituir os espaços reservados:


- seu\_servidor
- seu\_banco de dados
- seu\_usuário
- sua\_senha


## Etapa 3: Inserir linhas


Esse segmento Java emite uma instrução Transact-SQL INSERT para inserir duas linhas na tabela Person. Esta é a sequência geral:


1. Gere um objeto `PreparedStatement` usando o objeto `Connection`.
 - Incluímos o parâmetro `Statement.RETURN_GENERATED_KEYS` para que, posteriormente, você possa obter o valor gerado automaticamente para o valor de chave **id**.
2. Chame o método `execute` no objeto `PreparedStatement`.
3. Obtenha o valor numérico gerado automaticamente para a chave primária usando o objeto `PreparedStatement`.
 - Isso está relacionado à especificação de AUTO\_INCREMENT na coluna **id** da tabela Person


Copie e cole este pequeno segmento Java no exemplo de código principal, no local onde aparece o comentário `// INSERT two rows into the table.`.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";

	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## Etapa 4: Confirmar uma transação

O segmento de código Java a seguir emite uma instrução Transact-SQL UPDATE para aumentar o valor de `age` para cada linha na tabela de pessoas. Esta é a sequência geral:


1. O método `setAutoCommit` é chamado para evitar que as atualizações sejam automaticamente confirmadas no banco de dados.
2. O método `executeUpdate` é chamado para executar a ATUALIZAÇÃO no contexto da transação.
3. O método `commit` é chamado para confirmar explicitamente a transação.


Copie e cole este pequeno segmento Java no exemplo de código principal, no local onde aparece o comentário `// TRANSACTION and commit for an UPDATE.`.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);

	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);

	// Execute the statement.
	prepsUpdateAge.executeUpdate();

	//Commit the transaction.
	connection.commit();

	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## Etapa 4: Executar uma consulta


Esse segmento Java executa uma instrução Transact-SQL SELECT para ver todas as linhas atualizadas da tabela Person. Esta é a sequência geral:


1. Gere um objeto `Statement` usando o objeto `Connection`.
2. Gere um objeto `ResultSet` usando o objeto `Statement`.
3. Crie um loop de uma chamada para `resultSet.next` a fim de iterar por todas as linhas retornadas.


Copie e cole este pequeno segmento Java no exemplo de código principal, no local onde aparece o comentário `// SELECT rows from a table.`.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);

	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

## Próximas etapas

Para obter mais informações, consulte o [Centro de desenvolvedores do Java](/develop/java/).

<!---HONumber=AcomDC_0107_2016-->