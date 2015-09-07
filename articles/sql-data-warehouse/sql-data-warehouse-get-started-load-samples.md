<properties
   pageTitle="Carregar dados de exemplo no SQL Data Warehouse | Microsoft Azure"
	description="Carregar dados de exemplo no SQL Data Warehouse"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""/>

<tags
   ms.service="sql-data-warehouse"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-services"
	ms.date="08/05/2015"
	ms.author="lodipalm;barbkess"/>

#Carregar dados de amostra no SQL Data Warehouse

Agora que você configurou uma instância do SQL Data Warehouse, fica fácil carregar nela alguns dados de exemplo. A seguir, você receberá ajuda para criar um conjunto de dados chamado AdventureWorksPDW2012 no banco de dados. Esse conjunto de dados modela um exemplo de estrutura de data warehouse para uma empresa fictícia chamada AdventureWorks. Observe que você precisará do BCP instalado para as próximas etapas. Se o BCP não estiver instalado, instale os [Utilitários de linha de comando da Microsoft para SQL Server][].

1. Para começar, clique para baixar nossos [Exemplos de scripts de dados][].

2. Após o download do arquivo, extraia o conteúdo do arquivo AdventureWorksPDW2012.zip e abra a nova pasta AdventureWorksPDW2012.

3. Edite o arquivo aw\_create.bat e defina os seguintes valores na parte superior do arquivo:

   a. **Servidor**: o nome totalmente qualificado do servidor no qual o SQL Data Warehouse reside

   b. **Usuário**: o usuário para o servidor acima
   
   c. **Senha**: a senha para logon no servidor fornecido
   
   d. **Banco de dados**: o nome da instância do SQL Data Warehouse na qual você deseja carregar dados
   
   Certifique-se de que não exista nenhum espaço em branco entre o '=' e esses parâmetros.
   

4. Execute aw\_create.bat no diretório no qual ele está localizado. Isso criará o esquema e carregará os dados em todas as tabelas usando o BCP.


## Conectando-se e consultando seu exemplo

Conforme descrito na documentação de [conexão e consulta][], você pode se conectar a esse banco de dados usando o Visual Studio e o SSDT. Agora que você carregou alguns dados de exemplo no SQL Data Warehouse, você pode executar rapidamente algumas consultas para começar.

Podemos executar uma instrução select simples para obter todas as informações sobre os funcionários:

	SELECT * FROM DimEmployee;

Podemos executar também uma consulta mais complexa usando construções como GROUP BY para examinar o valor total de todas as vendas de cada dia:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Podemos até mesmo usar a cláusula WHERE para filtrar os pedidos realizados antes de uma determinada data:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Na verdade, o SQL Data Warehouse oferece suporte a quase todas as construções T-SQL com suporte do SQL Server, e você encontrará algumas das diferenças em nossa documentação sobre [migrar código][].

## Próximas etapas
Agora que proporcionamos a você um aquecimento com a verificação de dados de exemplo, confira como [desenvolver][], [carregar][] ou [migrar][].

<!--Image references-->

<!--Article references-->
[migrar]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-migrate/
[desenvolver]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-develop/
[carregar]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-load/
[conexão e consulta]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-get-started-connect-query/
[migrar código]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[Utilitários de linha de comando da Microsoft para SQL Server]: http://www.microsoft.com/pt-BR/download/details.aspx?id=36433

<!--Other Web references-->
[Exemplos de scripts de dados]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=August15_HO9-->