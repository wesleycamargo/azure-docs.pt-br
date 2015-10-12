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
   ms.date="09/23/2015"
   ms.author="lodipalm;barbkess"/>

#Carregar dados de amostra no SQL Data Warehouse

Durante a criação de sua instância do SQL Data Warehouse, também é possível carregar com facilidade alguns exemplos de dados. Se você ignorou esta etapa durante o provisionamento, também poderá [carregar dados de exemplo manualmente][].

Veja a seguir uma breve exibição de como o AdventureWorksDW pode ser carregado em seu banco de dados. Esse conjunto de dados modela um exemplo de estrutura de data warehouse para uma empresa fictícia chamada AdventureWorks, com dados que representam vendas e clientes da empresa.

## Adicionando dados de exemplo durante a criação
Você pode garantir o carregamento desses dados de exemplo em seu SQL Data Warehouse durante a implantação seguindo estas etapas:

1. Inicie o processo de criação localizando o SQL Data Warehouse no [Portal do Azure][] clicando em “+ Novo” e em “Dados e Armazenamento” ou no Marketplace, procurando “SQL Data Warehouse”. 
 
2. Após o início do processo, clique na opção ‘Selecionar origem’ e defina-o como ‘Exemplo’. Se você não estiver criando um novo servidor, também será necessário fornecer o logon do servidor que você está usando para criar.


> [AZURE.NOTE]Para carregar dados de exemplo em sua instância, será necessário habilitar os serviços do Azure para acessar o servidor (isso deverá estar ativado por padrão durante a criação de um novo servidor). Se isso não for feito, o carregamento falhará, mas você ainda poderá [carregar os dados de exemplo manualmente][].


##Usando o Power BI para analisar a Adventureworks

O uso do conjunto de dados de exemplo pode ser uma ótima maneira de começar a usar o Power BI. Depois de carregar os dados de exemplo, você poderá abrir uma conexão com o SQL Data Warehouse clicando no botão “Abrir no Power BI” no portal do Azure ou acessando [Power BI][] e [conectando-se ao SQL Data Warehouse][]. Após a conexão, um novo conjunto de dados deve ser criado com o mesmo nome de seu data warehouse. Para facilitar a análise, criamos uma exibição chamada ‘AggregateSales’ com algumas das métricas fundamentais para a análise das vendas da empresa. Você pode clicar no nome da exibição para expandi-la e ver as colunas que ela contém, e também pode criar algumas visualizações rápidas seguindo estas etapas:

1. Para começar, podemos criar com facilidade um mapa de todas as nossas vendas clicando nas colunas ‘PostalCode’ e ‘SalesAmount’. O Power BI reconhecerá isso automaticamente como dados geográficos e os colocará em um mapa para você. 

2. Agora, se você quiser criar um gráfico de barras das vendas, bastará clicar na coluna ‘SalesAmount’ e o Power BI o criará automaticamente. Você pode adicionar detalhes arrastando o gráfico ‘CustomerIncome’ para o campo ‘Eixo’ à esquerda de ‘AggregateSales’ para mostrar as vendas de acordo com a renda do cliente.

3. Por fim, se você quiser criar um cronograma de vendas, bastará clicar em ‘SalesAmount’, ‘OrderDate’ e ‘Gráfico de Linhas’ (o primeiro ícone na segunda linha sob “Visualizações”).

Você pode salvar seu andamento a qualquer momento clicando no botão ‘SALVAR’ no canto superior esquerdo e salvando suas visualizações como um relatório.

## Conectando-se e consultando seu exemplo

Você também pode analisar os dados de exemplo usando os meios tradicionais. Como descrito na documentação de [conexão e consulta][], você pode se conectar a esse banco de dados usando o SQL Server Data Tools no Visual Studio. Agora que você carregou alguns dados de exemplo no SQL Data Warehouse, você pode executar rapidamente algumas consultas para começar.

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

Na verdade, o SQL Data Warehouse dá suporte a quase todas as construções T-SQL com suporte do SQL Server, e você encontrará algumas das diferenças em nossa documentação sobre a [migração de código][].



## Próximas etapas
Agora que proporcionamos a você uma introdução à verificação de dados de exemplo, consulte como [desenvolver][], [carregar][] ou [migrar][].

<!--Image references-->

<!--Article references-->
[migrar]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-migrate/
[desenvolver]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-develop/
[carregar]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-load/
[conexão e consulta]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-get-started-connect-query/
[migração de código]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-migrate-code/
[carregar dados de exemplo manualmente]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[carregar os dados de exemplo manualmente]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Portal do Azure]: https://portal.azure.com
[Power BI]: http://www.powerbi.com
[conectando-se ao SQL Data Warehouse]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-integrate-power-bi/

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/pt-BR/download/details.aspx?id=36433

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=Oct15_HO1-->