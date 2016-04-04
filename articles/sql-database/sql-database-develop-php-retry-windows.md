<properties
	pageTitle="Lógica de repetição em PHP para se conectar ao Banco de Dados SQL | Microsoft Azure"
	description="Apresenta um programa PHP de exemplo que se conecta ao Banco de Dados SQL a partir de um cliente Windowscom tratamento de falhas transitórias e fornece links para os componentes de software necessários e exigidos pelo cliente."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="meetb"/>


# Conectar-se ao Banco de Dados SQL usando o PHP no Windows com o tratamento de falhas transitórias


[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico ilustra como você pode se conectar ao Banco de Dados SQL do Azure de um aplicativo cliente escrito em PHP executado no Windows.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### Um Banco de Dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.


## Etapa 1: Obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 2: Conectar e consultar

O programa de demonstração é projetado para que um erro transitório durante a tentativa de conexão leve a uma nova tentativa. Mas um erro transitório durante o comando de consulta faz com que o programa descarte a conexão e crie uma nova conexão, antes de tentar novamente o comando de consulta. Não recomendamos nem desaconselhamos essa opção de design. O programa de demonstração ilustra um pouco da flexibilidade de design que está disponível para você.

<br>O comprimento deste exemplo de código deve-se principalmente à lógica de exceção catch. Uma versão mais curta desse arquivo Program.cs está disponível [aqui](sql-database-develop-php-simple-windows.md). <br>O método Main está no Program.cs. A pilha de chamadas é executada da seguinte maneira:
* Main chama ConnectAndQuery.
* ConnectAndQuery chama EstablishConnection.
* EstablishConnection chama IssueQueryCommand.

A função [sqlsrv\_query()](http://php.net/manual/en/function.sqlsrv-query.php) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essencialmente, essa função aceita qualquer consulta e o objeto de conexão e retorna um conjunto de resultados que pode ser iterado com o uso de [sqlsrv\_fetch\_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	<?php
		// Variables to tune the retry logic.  
		$connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
		$maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
		$secondsBetweenRetries = 4;  // Simple retry strategy.
		$errNo = 0;
		$serverName = "tcp:yourdatabase.database.windows.net,1433";
		$connectionOptions = array("Database"=>"AdventureWorks",
		   "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
		$conn;
		$errorArr = array();
		for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
		{
		    $errorArr = array();
		    $ctr = 0;
		    // [A.2] Connect, which proceeds to issue a query command.
		    $conn = sqlsrv_connect($serverName, $connectionOptions);  
		    if( $conn == true)
		    {
		        echo "Connection was established";
		        echo "<br>";

		        $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
		        $getProducts = sqlsrv_query($conn, $tsql);
		        if ($getProducts == FALSE)
		            die(FormatErrors(sqlsrv_errors()));
		        $productCount = 0;
		        $ctr = 0;
		        while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
		        {   
		            $ctr++;
		            echo($row['CompanyName']);
		            echo("<br/>");
		            $productCount++;
		            if($ctr>10)
		                break;
		        }
		        sqlsrv_free_stmt($getProducts);
		        break;
		    }
		    // Adds any the error codes from the SQL Exception to an array.
		    else {  
		        if( ($errors = sqlsrv_errors() ) != null) {
		            foreach( $errors as $error ) {
		                $errorArr[$ctr] = $error['code'];
		                $ctr = $ctr + 1;
		            }
		        }
		        $isTransientError = TRUE;
		        // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
		        $isTransientError = IsTransientStatic($errorArr);
		        if ($isTransientError == TRUE)  // Is a static persistent error...
		        {
		            echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate.");
		            echo "<br>";
		            // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
		            // Break the loop, let the hopeless program end.
		            exit(0);
		        }
		        // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
		        // So let this method reloop and try again. However, we recommend that the new query
		        // attempt should start at the beginning and establish a new connection.
		        if ($cc >= $maxCountTriesConnectAndQuery)
		        {
		            echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
		            echo "<br>";
		            exit(0);
		        }
		        echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
		        echo "<br>";
		        echo $cc . " Attempts so far. Might retry.";
		        echo "<br>";
		        // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
		        sleep(1*$secondsBetweenRetries);
		    }
		    // [A.3] All has gone well, so let the program end.
		}
		function IsTransientStatic($errorArr) {
		    $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
		    $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
		    $count = count($result);
		    if($count >= 0) //change to > 0 later.
		        return TRUE;
		}
	?>

## Próximas etapas

Para saber mais sobre o uso e a instalação de PHP, confira [Acessando os Bancos de Dados SQL com PHP](http://technet.microsoft.com/library/cc793139.aspx).

<!---HONumber=AcomDC_0323_2016-->