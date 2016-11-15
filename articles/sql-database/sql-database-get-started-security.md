---
title: "Tutorial do Banco de Dados SQL: Introdução à segurança"
description: "Saiba como criar contas de usuário para acessar e gerenciar um banco de dados."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3bd8ac5466ae67df0a1865509c8fb897d1374ec


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Tutorial do Banco de Dados SQL: Criar contas de usuário do Banco de Dados SQL para acessar e gerenciar um banco de dados
> [!div class="op_single_selector"]
> * [Tutorial de introdução](sql-database-get-started-security.md)
> * [Conceder acesso](sql-database-manage-logins.md)
> 
> 

Neste tutorial, você aprenderá a usar o SQL Server Management Studio (SSMS) para:

* Faça logon no Banco de Dados SQL usando um logon de entidade no nível do servidor.
* Criar uma conta de usuário do Banco de Dados SQL.
* Conceda [permissões de db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) a um novo usuário de banco de dados SQL.
* Conecte-se a um Banco de Dados SQL com uma conta de usuário que não é uma entidade de nível de servidor.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial do Banco de Dados SQL, criou uma conta de usuário e atribuiu permissões dbo a ela, está pronto para saber mais sobre a [Segurança do Banco de Dados SQL](sql-database-manage-logins.md).




<!--HONumber=Nov16_HO2-->


