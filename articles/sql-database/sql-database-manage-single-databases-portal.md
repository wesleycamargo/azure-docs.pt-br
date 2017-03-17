---
title: "Portal do Azure: Criar e gerenciar bancos de dados SQL do Azure únicos | Microsoft Docs"
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
ms.sourcegitcommit: 5e01db2676e3515f41bf98d23595e6509c0d6805
ms.openlocfilehash: c5f50213be9cd20c82acf8dd94463e7dce0a0195
ms.lasthandoff: 02/27/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Criar e gerenciar bancos de dados SQL do Azure únicos com o portal do Azure

Você pode criar e gerenciar um banco de dados SQL do Azure com o [portal do Azure](https://portal.azure.com/), o PowerShell, o Transact-SQL, a API REST ou C#. Este tópico mostra como usar o portal do Azure. Para o PowerShell, confira [Criar e gerenciar bancos de dados únicos com o Powershell](sql-database-manage-single-databases-powershell.md). Para o Transact-SQL, confira [Criar e gerenciar bancos de dados únicos com o Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Criar um único banco de dados do SQL Azure com o portal do Azure

1. Abra a folha **Banco de Dados SQL** no [Portal do Azure](https://portal.azure.com/). 

    ![bancos de dados sql](./media/sql-database-get-started/sql-databases.png)
2. Na folha dos Bancos de dados SQL, clique em **Adicionar**.

    ![adicionar banco de dados sql](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Para obter um tutorial sobre a criação de um banco de dados usando o portal do Azure, confira [Criar um banco de dados - portal do Azure](sql-database-get-started.md).
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Exibir e atualizar as configurações de Banco de Dados SQL usando o Portal do Azure

1. Abra a folha **Banco de Dados SQL** no [Portal do Azure](https://portal.azure.com/). 

    ![bancos de dados sql](./media/sql-database-get-started/sql-databases.png)

2. Clique no Banco de Dados com o qual você deseja trabalhar e, em seguida, clique na configuração desejada na folha de Banco de Dados SQL.

    ![nova folha de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Alterar a camada de serviço e o nível de desempenho de um banco de dados único

Abra a folha Banco de Dados SQL do banco de dados que você deseja escalar verticalmente:

1. No [Portal do Azure](https://portal.azure.com), clique em **Mais serviços** > **Bancos de Dados SQL**.
2. Clique no banco de dados que você deseja alterar.
3. Na folha **Banco de Dados SQL**, clique em **Tipo de preço (DTUs em escala)**:
   
   ![Tipo de preço](./media/sql-database-manage-single-database-portal/new-tier.png)

4. Selecione uma nova camada e clique em **Selecionar**:
   
   Clicar em **Selecionar** envia uma solicitação de escala para alterar o tipo de preço. Dependendo do tamanho do banco de dados, a operação de escala poderá demorar um pouco para ser concluída (veja [Camadas de serviço](sql-database-service-tiers.md).
   
   > [!NOTE]
   > Alterar o tipo de preço do banco de dados não altera o tamanho máximo do banco de dados. Para alterar o tamanho máximo do banco de dados, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   >  
   
   ![selecionar camada de preços](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. Clique no ícone de notificação (sino), no canto superior direito:
   
   ![Notificações](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. Clique no texto da notificação para abrir o painel de detalhes, onde você poderá ver o status da solicitação.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral das ferramentas de gerenciamento, consulte [Visão geral das ferramentas de gerenciamento](sql-database-manage-overview.md).
* Para saber como realizar tarefas de gerenciamento usando o Portal do Azure, consulte [Gerenciar Bancos de Dados SQL do Azure usando o Portal do Azure](sql-database-manage-portal.md).
* Para saber como realizar tarefas de gerenciamento usando o PowerShell, consulte [Gerenciar Bancos de Dados SQL do Azure usando o PowerShell](sql-database-manage-powershell.md).
* Para saber como realizar tarefas de gerenciamento usando o SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obter informações sobre o serviço do Banco de Dados SQL, consulte [O que é o Banco de Dados SQL](sql-database-technical-overview.md). 
* Para obter informações sobre os servidores de Banco de Dados do Azure e recursos de banco de dados, confira [Recursos](sql-database-features.md).

