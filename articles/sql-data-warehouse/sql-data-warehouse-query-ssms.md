---
title: "Conectar-se ao SQL Data Warehouse do Azure – SSMS | Microsoft Docs"
description: Use o SQL Server Management Studio (SSMS) para conectar e consultar o SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: 
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 299e50b3-e68a-471c-8aee-b0b9874781bd
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/31/2016
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 77474214c6fafe7f591030d30f6a46c66fbc5c09
ms.openlocfilehash: 30ac3558534e96b63d78f9c66d42d11b7c1a3c75


---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Conectar-se ao SQL Data Warehouse com o SSMS (SQL Server Management Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Aprendizado de Máquina do Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o SQL Server Management Studio (SSMS) para conectar e consultar o SQL Data Warehouse. 

## <a name="prerequisites"></a>Pré-requisitos
Para usar este tutorial, você precisará de:

* Um SQL Data Warehouse existente. Para criar um, confira [Criar um SQL Data Warehouse][Criar um SQL Data Warehouse].
* SSMS (SQL Server Management Studio) instalado. [Instalar SSMS][Instalar SSMS] gratuitamente se você ainda não o tiver.
* O nome de servidor SQL totalmente qualificado. Para encontrar isso, confira [Conectar-se ao SQL Data Warehouse][Conectar-se ao SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Conectar-se ao SQL Data Warehouse
1. Abra o SSMS.
2. Abrir Pesquisador de Objetos. Para fazer isso, selecione **Arquivo** > **Conectar Pesquisador de Objetos**.
   
    ![Pesquisador de Objetos do SQL Server][1]
3. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor][2]
   
   * **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Usuário** e **Senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
4. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb][3]

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo
Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma nova janela de consulta é aberta.
   
    ![Nova Consulta][4]
3. Copie esta consulta TSQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para fazer isso, clique em `Execute` ou use o atalho a seguir: `F5`.
   
    ![Executar consulta][5]
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta][6]

## <a name="next-steps"></a>Próximas etapas
Agora que você pode se conectar e consultar, tente [visualizar os dados com o PowerBI][visualizar os dados com o PowerBI].

Para configurar seu ambiente para a autenticação do Azure Active Directory, confira [Autenticar no SQL Data Warehouse][Autenticar no SQL Data Warehouse].

<!--Arcticles-->
[Conectar-se ao SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Autenticar no SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizar os dados com o PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Portal do Azure]: https://portal.azure.com
[Instalar SSMS]: https://msdn.microsoft.com/en-US/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png



<!--HONumber=Nov16_HO3-->


