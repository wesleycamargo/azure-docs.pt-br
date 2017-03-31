---
title: Copiar um Banco de Dados SQL do Azure usando o Transact-SQL | Microsoft Docs
description: "Criar cópia de um Banco de Dados SQL do Azure usando o Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 40ea3718-33f8-41af-90cb-3aa15059365e
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
ms.openlocfilehash: e8d71d7b6b4302f177732381ad19e6a645bc5474
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copiar um banco de dados SQL do Azure usando o Transact-SQL

As etapas a seguir mostram como copiar um banco de dados SQL com Transact-SQL para o mesmo servidor ou outro servidor. A operação de cópia do banco de dados usa a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . 

> [!NOTE]
> Também é possível criar um Banco de Dados SQL usando o [Portal do Azure](sql-database-copy-portal.md)ou o [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md).
>

Para concluir as etapas neste artigo, você precisa do seguinte:

* Uma assinatura do Azure. Caso você precise de uma assinatura do Azure, basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
* Um banco de dados SQL Azure. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
* [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Se você não tiver o SSMS, ou se os recursos descritos neste artigo não estiverem disponíveis, [Baixe a versão mais recente](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="copy-your-sql-database"></a>Copiar seu banco de dados SQL
Faça logon no banco de dados mestre usando o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager para copiar os bancos de dados. Para saber mais sobre logons e como se conectar ao servidor, confira [Gerenciar logons](sql-database-manage-logins.md).

Inicie a cópia do banco de dados de origem com a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . A execução dessa instrução inicia o processo de cópia do banco de dados. Como a cópia um banco de dados é um processo assíncrono, a instrução CREATE DATABASE retorna antes da conclusão da cópia do banco de dados.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados SQL para o mesmo servidor
Faça logon no banco de dados mestre usando o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager para copiar os bancos de dados.

Esse comando copia o Database1 para um novo banco de dados chamado Database2 no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente
Faça logon no banco de dados mestre do servidor de destino, o servidor do Banco de Dados SQL do Azure, onde o novo banco de dados deve ser criado. Use um logon que tenha o mesmo nome e senha como o proprietário do banco de dados (DBO) do banco de dados de origem no servidor do Banco de Dados SQL do Azure de origem. O logon no servidor de destino também deve ser membro da função dbmanager ou ser o logon principal no nível de servidor.

Esse comando copia o Database1 no server1- para um novo banco de dados chamado Database2 no server2. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorar o andamento da operação de cópia
Monitore o processo de cópia consultando as exibições sys.databases e sys.dm_database_copies. Enquanto a cópia estiver em andamento, a coluna state_desc da exibição sys.databases para o novo banco de dados é definida como COPYING.

* Se a cópia falhar, a coluna state_desc da exibição sys.databases para o novo banco de dados será definida como SUSPECT. Nesse caso, execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
* Se a cópia for bem-sucedida, a coluna state_desc da exibição sys.databases para o novo banco de dados será definida como ONLINE. Nesse caso, a cópia foi concluída e o novo banco de dados é um banco de dados normal, capaz de ser alterado de forma independente do banco de dados de origem.

> [!NOTE]
> * Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) no novo banco de dados. Como alternativa, a execução da instrução DROP DATABASE no banco de dados de origem também cancelará o processo de cópia.
> 
> 

## <a name="resolve-logins-after-the-copy-operation-completes"></a>Resolver logons após a conclusão da operação de cópia
Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para remapear os usuários do novo banco de dados para logons no servidor de destino. Para resolver usuários órfãos, confira [Solução de problemas de usuários órfãos](https://msdn.microsoft.com/library/ms175475.aspx). Veja também [Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Todos os usuários no novo banco de dados mantêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se tornará o proprietário do novo banco de dados e receberá um novo identificador de segurança (SID). Depois que a cópia for bem-sucedida e antes que outros usuários sejam remapeados, somente o logon que tiver iniciado a cópia, o proprietário do banco de dados (DBO), poderá fazer logon no novo banco de dados.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor lógico diferente, confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

