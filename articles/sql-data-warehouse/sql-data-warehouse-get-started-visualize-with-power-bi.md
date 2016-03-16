<properties
   pageTitle="Visualizar os dados do SQL Data Warehouse com o Power BI | Microsoft Azure"
   description="Visualizar os dados do SQL Data Warehouse com o Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# Visualizar os dados com o Power BI


> [AZURE.NOTE]Para concluir este tutorial, você precisa de um banco de dados do SQL Data Warehouse que é pré-carregado com o banco de dados de exemplo AdventureWorksDW. [Criar um SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) mostra como criar um.
> 
> Se você já tiver um banco de dados do SQL Data Warehouse, mas não tiver dados de exemplo, será possível [carregar dados de exemplo manualmente][].

Este tutorial mostra como usar o Power BI para se conectar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Conectar-se ao AdventureWorksDW

Para abrir o Power BI e conectar-se ao banco de dados AdventureWorksDW:

1. Entre no [Portal do Azure][].
2. Clique em **bancos de dados SQL** e escolha o banco de dados AdventureWorks do SQL Data Warehouse. 

    ![Localize o banco de dados][1]

3. Clique no botão “Abrir no Power BI”.

    ![Botão Power BI][2]

4. Agora você deve ver a página de conexão do SQL Data Warehouse exibindo o endereço Web do seu banco de dados. Clique em Avançar.

    ![Conexão do Power BI][3]

6. Insira seu nome de usuário e senha do servidor do Azure SQL e você será totalmente conectado ao banco de dados do SQL Data Warehouse.

    ![Entrada do Power BI][4]

1. Depois de entrar no Power BI, clique no conjunto de dados do AdventureWorksDW na folha esquerda. Isso abrirá o banco de dados.

    ![Abrir AdventureWorksDW no Power BI][5]



## Criar um relatório do Power BI para analisar os dados de exemplo

Agora você está pronto para usar o Power BI para analisar os dados de exemplo do AdventureWorksDW. Para executar a análise, o AdventureWorksDW possui uma exibição chamada AggregateSales. Essa exibição contém algumas das principais métricas para analisar as vendas da empresa.

1. Para criar um mapa do valor de vendas de acordo com o CEP, clique no painel de campos à direita, clique na exibição AggregateSales para expandi-la. Clique nas colunas PostalCode e SalesAmount para selecioná-las. 

    ![Selecionar AggregateSales no Power BI][6]

    O Power BI reconhecerá isso automaticamente como dados geográficos e os colocará em um mapa para você.

    ![Mapa do Power BI][7]

2. Esta etapa cria um gráfico de barras que mostra o valor de vendas por receita de cliente. Para criar isso, vá para a exibição expandida AggregateSales. Clique no campo SalesAmount. Arraste o campo Receita de Cliente para a esquerda e solte-o no Eixo.
    
    ![Selecionar eixo no Power BI][8]

    Mudamos o gráfico de barras para o lado esquerdo.

    ![Barra do Power BI][9]

3. Esta etapa cria um gráfico de linhas que mostra o valor de vendas por data do pedido. Para criar isso, vá para a exibição expandida AggregateSales. Clique em SalesAmount e em OrderDate. Na coluna Visualizações, clique no ícone Gráfico de Linhas; é o primeiro ícone na segunda linha sob visualizações.

	![Selecionar gráfico de linhas no Power BI][10]

    Agora você tem um relatório que mostra três visualizações diferentes dos dados.

    ![Linha do Power BI][11]

Você pode salvar seu andamento a qualquer momento clicando em **Arquivo** e selecionando **Salvar**.

## Próximas etapas
Agora que proporcionamos a você uma introdução à verificação de dados de exemplo, veja como [desenvolver][], [carregar][] ou [migrar][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-overview-load.md
[carregar dados de exemplo manualmente]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Portal do Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=AcomDC_0114_2016-->