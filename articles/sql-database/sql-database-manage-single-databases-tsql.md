---
title: "T-SQL: criar e gerenciar bancos de dados únicos SQL do Azure | Microsoft Docs"
description: "Referência rápida sobre como criar e gerenciar um banco de dados único SQL do Azure usando o portal do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 38c53d6f1a2635afbba199e6f0a2adb23abb6a3a
ms.openlocfilehash: fcf8326edc2ff84d274f7c4dadeae1fbe2595349


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Criar e gerenciar bancos de dados únicos SQL do Azure com o Transact-SQL

Você pode criar e gerenciar um banco de dados SQL do Azure com o [Portal do Azure](https://portal.azure.com/), o PowerShell, o Transact-SQL, a API REST ou C#. Este tópico mostra como usar o portal do Azure. Para o PowerShell, confira [Criar e gerenciar bancos de dados únicos com o Powershell](sql-database-manage-single-databases-powershell.md). Para o Transact-SQL, confira [Criar e gerenciar bancos de dados únicos com o Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Criar um Banco de Dados SQL do Azure usando o Transact-SQL no SQL Server Management Studio

Para criar um Banco de Dados SQL usando o Transact-SQL no SQL Server Management Studio:

1. Usando o SQL Server Management Studio, conecte-se com o servidor de Banco de Dados do Azure usando o logon da entidade de segurança no nível do servidor ou um logon que seja membro da função **dbmanager**. Para obter mais informações sobre logons, consulte [Gerenciar logons](sql-database-manage-logins.md).
2. Em Pesquisador de Objetos, abra o nó Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a instrução **CREATE DATABASE** para criar um banco de dados. Para saber mais, confira [CREATE DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/dn268335.aspx). A instrução a seguir cria um banco de dados chamado **myTestDB** e especifica que se trata de um banco de dados da Standard S0 Edition com um tamanho padrão máximo de 250 GB.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Clique em **Execute** para executar a consulta.
5. No Pesquisador de Objetos, clique com botão direito do mouse no nó Bancos de Dados e clique em **Atualizar** para exibir o novo banco de dados no Pesquisador de Objetos. 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Alterar a camada de serviço e o nível de desempenho de um banco de dados único
Altere o tamanho máximo de seu banco de dados usando [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx)

> [!TIP]
> Para obter um tutorial sobre a criação de um banco de dados usando o Transact-SQL, confira [Criar um banco de dados - portal do Azure](sql-database-get-started.md).
>

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral das ferramentas de gerenciamento, consulte [Visão geral das ferramentas de gerenciamento](sql-database-manage-overview.md).
* Para saber como realizar tarefas de gerenciamento usando o Portal do Azure, consulte [Gerenciar Bancos de Dados SQL do Azure usando o Portal do Azure](sql-database-manage-portal.md).
* Para saber como realizar tarefas de gerenciamento usando o PowerShell, consulte [Gerenciar Bancos de Dados SQL do Azure usando o PowerShell](sql-database-manage-powershell.md).
* Para saber como realizar tarefas de gerenciamento usando o SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obter informações sobre o serviço do Banco de Dados SQL, consulte [O que é o Banco de Dados SQL](sql-database-technical-overview.md). 
* Para obter informações sobre os servidores de Banco de Dados do Azure e recursos de banco de dados, confira [Recursos](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


