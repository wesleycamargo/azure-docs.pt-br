---
title: 'SSMS: Conectar e consultar dados no Banco de Dados SQL do Azure | Microsoft Docs'
description: "Saiba como se conectar a um banco de dados SQL no Azure usando o SSMS (SQL Server Management Studio). Em seguida, execute as instruções Transact-SQL (T-SQL) para consultar e editar dados."
metacanonical: 
keywords: conectar-se ao banco de dados sql, sql server management studio
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: carlrab
ms.openlocfilehash: 6ed77d5c106f72a06e410410321c39b3d7059ae9
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: Use o SQL Server Management Studio para conectar e consultar dados

[SQL Server Management Studio][ssms-install-latest-84g] (SSMS) é um ambiente integrado para gerenciar qualquer infraestrutura do SQL, do SQL Server para o Banco de Dados SQL do Microsoft Windows. Este guia rápido demonstra como usar o SSMS para conectar um banco de dados SQL do Azure, em seguida, usar instruções Transact-SQL para consultar, inserir, atualizar e excluir os dados no banco de dados. 

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-the-latest-ssms"></a>Instalar o SSMS mais recente

Antes de começar, verifique se você instalou a versão mais recente do [SSMS][ssms-install-latest-84g]. 

## <a name="sql-server-connection-information"></a>Informações de conexão do servidor SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

Use o SQL Server Management Studio para estabelecer uma conexão com seu servidor de Banco de Dados SQL do Azure. 

> [!IMPORTANT]
> Um servidor lógico do Banco de Dados SQL do Azure escuta na porta 1433. Se você estiver tentando conectar um servidor lógico do Banco de Dados SQL do Azure de dentro de um firewall corporativo, essa porta deverá estar aberta no firewall corporativo para que você possa conectar-se com êxito.
>

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

   | Configuração      | Valor sugerido    | Descrição | 
   | ------------ | ------------------ | ----------- | 
   | **Tipo de servidor** | Mecanismo de banco de dados | Esse valor é obrigatório. |
   | **Nome do servidor** | O nome do servidor totalmente qualificado | O nome deve ser semelhante como: **mynewserver20170313.database.windows.net**. |
   | **Autenticação** | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | **Logon** | A conta do administrador do servidor | Esta é a conta que você especificou quando criou o servidor. |
   | **Senha** | A senha para sua conta do administrador do servidor | Esta é a senha que você especificou quando criou o servidor. |
   ||||

   ![conectar-se ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Clique em **Opções** na caixa de diálogo **Conectar servidor**. Na seção **Conectar ao banco de dados**, digite **mySampleDatabase** para conectar-se a este banco de dados.

   ![conectar o banco de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Conectar**. A janela Pesquisador de Objetos abre no SSMS. 

   ![conectado ao servidor](./media/sql-database-connect-query-ssms/connected.png)  

5. No Pesquisador de Objetos, expanda **Bancos de Dados** e expanda **mySampleDatabase** para exibir os objetos no banco de dados de exemplo.

## <a name="query-data"></a>Consultar dados

Use o seguinte código para consultar os 20 principais produtos por categoria usando a instrução [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) do Transact-SQL.

1. No Pesquisador de Objetos, clique com o botão direito em **mySampleDatabase** e clique em **Nova Consulta**. Uma janela de consulta em branco conectada ao seu banco de dados é aberta.
2. Na janela de consulta, insira a seguinte consulta:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, clique em **Executar** para recuperar dados das tabelas Product e ProductCategory.

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>Inserir dados

Use o código a seguir para inserir um novo produto na tabela SalesLT.Product usando a instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) do Transact-SQL.

1. Na janela de consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Na barra de ferramentas, clique em **Executar** para inserir uma nova linha na tabela Product.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Atualizar dados

Use o código a seguir para atualizar o novo produto que você adicionou anteriormente usando a instrução [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) do Transact-SQL.

1. Na janela de consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar** para atualizar a linha especificada na tabela Product.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Excluir dados

Use o código a seguir para excluir o novo produto que você adicionou anteriormente usando a instrução [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) do Transact-SQL.

1. Na janela de consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar** para excluir a linha especificada na tabela Product.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como criar e gerenciar servidores e bancos de dados com o Transact-SQL, confira [Saiba mais sobre servidores de bancos de dados e banco de dados SQL do Azure](sql-database-servers-databases.md).
- Para saber mais sobre o SSMS, consulte [Usar o SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para se conectar e consultar usando o Portal do Azure, consulte [Conectar e consultar com o editor de consultas do SQL do Portal do Azure](sql-database-connect-query-portal.md).
- Para conectar e consultar usando o Visual Studio Code, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/en-us/sql/ssms/sql-server-management-studio-ssms

