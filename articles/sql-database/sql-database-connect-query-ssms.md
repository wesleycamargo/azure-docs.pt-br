---
title: "Conectar-se ao banco de dados SQL ‒ SQL Server Management Studio | Microsoft Docs"
description: Saiba como se conectar a um banco de dados SQL no Azure usando o SSMS (SQL Server Management Studio). Em seguida, execute uma consulta de exemplo usando o Transact-SQL (T-SQL).
metacanonical: 
keywords: conectar-se ao banco de dados sql, sql server management studio
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0eb25eb76c6c6c2446ac0b2b07c65975c3719db0


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-tsql-query"></a>Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Este artigo mostra como se conectar a um banco de dados SQL do Azure usando o SQL Server Management Studio (SSMS). Depois de nos conectarmos com êxito, executamos uma consulta Transact-SQL (T-SQL) simples para verificar a comunicação com o banco de dados.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[!INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

## <a name="run-sample-queries"></a>Executar consultas de exemplo
Depois que se conectar ao servidor lógico, você poderá se conectar a um banco de dados e executar uma consulta de exemplo. Se você for inexperiente na gravação de consultas, veja [Escrevendo instruções Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx).

1. Em **Pesquisador de Objetos**, navegue até um banco de dados no servidor, como o exemplo de banco de dados **AdventureWorks**.
2. Clique com o botão direito do mouse no banco de dados e selecione **Nova Consulta**:
   
    ![Nova consulta. Conectar-se ao servidor de Banco de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)
3. Na janela de consulta, copie e cole o seguinte:
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
4. Clique no botão **Executar** :
   
    ![Sucesso. Conectar-se ao servidor de Banco de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## <a name="next-steps"></a>Próximas etapas
Você pode usar instruções T-SQL para criar e gerenciar bancos de dados no Azure da mesma forma que faz no SQL Server. Se você já sabe como usar o T-SQL com o SQL Server, consulte [Informações de Transact-SQL de Banco de Dados SQL do Azure](sql-database-transact-sql-information.md) para obter um resumo das diferenças.

Se não tiver experiência com o T-SQL, consulte [Tutorial: Escrevendo instruções Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e a [Referência do Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx).

Para começar a criação de usuários de banco de dados e administradores de usuários de banco de dados, confira [Introdução à segurança do Banco de Dados SQL](sql-database-get-started-security.md)

Para saber mais sobre o SSMS, consulte [Usar o SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Nov16_HO2-->


