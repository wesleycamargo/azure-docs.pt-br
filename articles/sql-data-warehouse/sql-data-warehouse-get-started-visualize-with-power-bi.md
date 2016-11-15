---
title: Visualizar os dados do SQL Data Warehouse com o Power BI | Microsoft Azure
description: Visualizar os dados do SQL Data Warehouse com o Power BI
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f498f4546e8c23f2141d8d30160a360fa0fc2514


---
# <a name="visualize-data-with-power-bi"></a>Visualizar os dados com o Power BI
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Aprendizado de Máquina do Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Este tutorial mostra como usar o Power BI para se conectar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar este tutorial, você precisará:

* Um SQL Data Warehouse pré-carregado com o banco de dados AdventureWorksDW. Para provisionar isso, consulte [Criar um SQL Data Warehouse][Criar um SQL Data Warehouse] e opte por carregar os dados de exemplo. Se você já tiver um data warehouse, mas não tiver dados de exemplo, poderá [carregar dados de exemplo manualmente][carregar dados de exemplo manualmente].

## <a name="1-connect-to-your-database"></a>1. Conectar-se ao seu banco de dados
Para abrir o Power BI e conectar-se ao banco de dados AdventureWorksDW:

1. Entre no [Portal do Azure][Portal do Azure].
2. Clique em **bancos de dados SQL** e escolha o banco de dados AdventureWorks do SQL Data Warehouse.
   
    ![Localize o banco de dados][1]
3. Clique no botão “Abrir no Power BI”.
   
    ![Botão Power BI][2]
4. Agora você deve ver a página de conexão do SQL Data Warehouse exibindo o endereço Web do seu banco de dados. Clique em Avançar.
   
    ![Conexão do Power BI][3]
5. Insira seu nome de usuário e senha do servidor do Azure SQL e você será totalmente conectado ao banco de dados do SQL Data Warehouse.
   
    ![Entrada do Power BI][4]
6. Depois de entrar no Power BI, clique no conjunto de dados do AdventureWorksDW na folha esquerda. Isso abrirá o banco de dados.
   
    ![Abrir AdventureWorksDW no Power BI][5]

## <a name="2-create-a-report"></a>2. Criar um relatório
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

## <a name="next-steps"></a>Próximas etapas
Agora que proporcionamos a você um aquecimento com os dados de exemplo, veja como [desenvolver][desenvolver], [carregar][carregar], ou [migrar][migrar]. Ou confira o [Site do Power BI][Site do Power BI].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolvê-los]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de exemplo manualmente]: sql-data-warehouse-load-sample-databases.md
[conectando-se ao SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com/
[Site do Power BI]: http://www.powerbi.com/



<!--HONumber=Nov16_HO2-->


