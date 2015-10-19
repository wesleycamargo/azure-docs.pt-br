<properties
    pageTitle="Analisar dados do SQL Data Warehouse com o Power BI | Microsoft Azure"
    description="Analisar dados do SQL Data Warehouse com o Power BI"
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
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Analisar dados com o Power BI
Este tutorial mostra como usar o Power BI para se conectar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [AZURE.NOTE]Para concluir este tutorial, você precisa de um banco de dados do SQL Data Warehouse que é pré-carregado com o banco de dados de exemplo AdventureWorksDW. [Criar um SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) mostra como criar um.
> 
> Se você já tiver um banco de dados SQL Data Warehouse mas não possui dados de exemplo, você pode [carregar dados de exemplo manualmente][].


## Conecte-se ao banco de dados SQL Data Warehouse

Para abrir o Power BI e conectar-se ao banco de dados AdventureWorksDW:

1. Entre no [portal de visualização do Azure][].
2. Clique em **bancos de dados SQL** e escolha o banco de dados SQL Data Warehouse AdventureWorks. 

    ![Localize o banco de dados][1]

3. Clique no botão “Abrir no Power BI”.

    ![Botão Power BI][2]

4. Agora você deve ver a página de conexão do SQL Data Warehouse exibindo suas informações de banco de dados. Insira sua senha e você será totalmente conectado ao banco de dados SQL Data Warehouse.


## Analisar dados com o Power BI

Agora você está pronto para usar o Power BI para analisar os dados de exemplo do AdventureWorksDW. Para executar a análise, o AdventureWorksDW possui uma exibição chamada AggregateSales. Essa exibição contém algumas das principais métricas para analisar as vendas da empresa.

1. Para criar um mapa do valor de vendas de acordo com o CEP, clique na exibição AggregateSales para expandi-la e clique nas colunas de PostalCode e SalesAmount. O Power BI reconhecerá isso automaticamente como dados geográficos e os colocará em um mapa para você.

    ![Mapa do Power BI][3]

2. Para criar um gráfico de barras de vendas, basta clicar na coluna SalesAmount. Para adicionar detalhes, arraste o gráfico CustomerIncome para o campo Eixo à esquerda de AggregateSales para mostrar as vendas de acordo com a renda do cliente.

    ![Barra do Power BI][4]

3. Para criar um cronograma de vendas, clique em SalesAmount, OrderDate e Gráfico de Linhas que é o primeiro ícone na segunda linha abaixo de Visualizações.

    ![Linha do Power BI][5]

Você pode salvar seu andamento a qualquer momento clicando no botão **SALVAR** no canto superior esquerdo e salvando suas visualizações como um relatório.

## Próximas etapas
Agora que proporcionamos a você uma introdução à verificação de dados de exemplo, veja como [desenvolver][], [carregar][] ou [migrar][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de exemplo manualmente]: sql-data-warehouse-get-started-manually-load-samples.md
[portal de visualização do Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO2-->