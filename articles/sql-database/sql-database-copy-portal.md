---
title: Copiar um banco de dados SQL do Azure usando o Portal do Azure | Microsoft Docs
description: "Criar uma cópia de um Banco de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 752dcb97101f4a8288125e3e41167095620d130f
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copiar um Banco de Dados SQL do Azure usando o Portal do Azure

As etapas a seguir mostram como copiar um banco de dados SQL com o [portal do Azure](https://portal.azure.com) para o mesmo ou outro servidor. 

> [!NOTE]
> Também é possível copiar um Banco de Dados SQL usando o [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) ou o [Transact-SQL](sql-database-copy-transact-sql.md).
>

Para copiar um banco de dados SQL, você precisa dos seguintes itens:

* Uma assinatura do Azure. Caso você precise de uma assinatura do Azure, basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
* Um banco de dados SQL para copiar. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

## <a name="copy-your-sql-database"></a>Copiar seu banco de dados SQL
Abra a página do banco de dados SQL do banco de dados que você deseja copiar:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Clique em **Mais serviços** > **Bancos de dados SQL** e clique no banco de dados desejado.
3. Na página do banco de dados SQL, clique em **Copiar**:
   
   ![Banco de Dados SQL](./media/sql-database-copy-portal/sql-database-copy.png)
4. Na página **Copiar** , é fornecido um nome de banco de dados padrão. Digite um nome diferente se desejar (todos os bancos de dados em um servidor devem ter nomes exclusivos).
5. Selecione um **Servidor de destino**. A cópia do banco de dados é criada no servidor de destino. Você pode copiar o banco de dados no mesmo servidor ou em outro servidor. Você pode criar um servidor ou escolher um servidor existente na lista. 
6. Depois que você escolher o **Servidor de destino**, o **pool elástico** e o **Tipo de preço**, as opções serão habilitadas. Se o servidor tiver um pool, você poderá copiar o banco de dados nele.
7. Clique em **OK** para iniciar o processo de cópia.
   
   ![Banco de Dados SQL](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorar o andamento da operação de cópia
* Depois de iniciar a cópia, clique na notificação de portal para obter detalhes.
  
    ![notificação][3]
  
    ![monitor][4]

## <a name="verify-the-database-is-live-on-the-server"></a>Verificar se o banco de dados reside no servidor
* Clique em **Mais serviços** > **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="resolve-logins"></a>Resolver logons
Para resolver logons após a conclusão da operação de cópia, confira [Resolver logons](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor lógico diferente, confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para exportar um banco de dados para um arquivo BACPAC usando o Portal do Azure, veja [Exportar o banco de dados para um BACPAC usando o Portal do Azure](sql-database-export-portal.md).
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->

[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


