<properties
   pageTitle="Conectar-se ao SQL Data Warehouse com o Visual Studio | Microsoft Azure"
   description="Introdução ao SQL Data Warehouse e execução de algumas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/09/2016"
   ms.author="sonyama;barbkess"/>

# Conectar-se ao SQL Data Warehouse com o Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

Este passo a passo mostra como conectar um SQL Data Warehouse do Azure em apenas alguns minutos usando a extensão SSDT (SQL Server Data Tools) no Visual Studio. Uma vez conectado, você executará uma consulta simples.

## Pré-requisitos

+ Dados de exemplo do AdventureWorksDW no SQL Data Warehouse. Para criar isso, consulte [Criar um SQL Data Warehouse][].
+ SQL Server Data Tools para Visual Studio Para obter instruções e opções de instalação, consulte [Instalar o Visual Studio e o SSDT][].

## Etapa 1: Encontrar o nome totalmente qualificado do servidor do SQL Azure

O banco de dados SQL Data Warehouse está associado a um SQL Server do Azure. Para conectar o banco de dados, você precisa do nome totalmente qualificado do servidor (**nomeservidor**.database.windows.net*).

Para localizar o nome de servidor totalmente qualificado.

1. Vá para o [Portal do Azure][].
2. Clique em **Bancos de dados SQL** e clique no banco de dados ao qual você deseja se conectar. Este exemplo usa o banco de dados de exemplo AdventureWorksDW.
3. Localize o nome completo do servidor.

    ![Nome completo do servidor][1]

## Etapa 2: Conectar o SQL Data Warehouse

1. Abra o Visual Studio 2013 ou 2015.
2. Abra o Pesquisador de Objetos do SQL Server. Para fazer isso, selecione **Exibir** > **Pesquisador de Objetos do SQL Server**.

    ![Pesquisador de Objetos do SQL Server][2]

3. Clique no ícone **Adicionar SQL Server**.

    ![Adicionar o SQL Server][3]

4. Preencha os campos na janela Conectar ao Servidor.

    ![Conectar-se ao servidor][4]

    - **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
    - **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
    - **Nome de Usuário** e **Senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
    - Clique em **Conectar**.

5. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.

    ![Explorar o AdventureWorksWeb][5]

## Etapa 3: Executar uma consulta de exemplo

Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.

2. Selecione **Nova Consulta**. Uma nova janela de consulta é aberta.

    ![Nova consulta][6]

3. Copie esta consulta TSQL na janela de consulta:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Execute a consulta. Para fazer isso, clique na seta verde ou use este atalho: `CTRL`+`SHIFT`+`E`.

    ![Executar consulta][7]

5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.

    ![Resultados da consulta][8]

## Próximas etapas

Agora que você pode se conectar e consultar, tente [visualizar os dados com o PowerBI][].

Para configurar seu ambiente para a autenticação do Windows, consulte [Conectar Banco de Dados SQL ou SQL Data Warehouse Usando a Autenticação do Azure Active Directory][].

<!--Arcticles-->
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Instalar o Visual Studio e o SSDT]: sql-data-warehouse-install-visual-studio.md
[Conectar Banco de Dados SQL ou SQL Data Warehouse Usando a Autenticação do Azure Active Directory]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[visualizar os dados com o PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0615_2016-->