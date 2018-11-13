---
title: Restaurar um backup na Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure usando SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: bc27ece2eddc842a81698aaa685cbe6d63c6a1df
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912247"
---
# <a name="quickstart-restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Início Rápido: Restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure

Este início rápido demonstra como restaurar um backup de um banco de dados armazenado no Armazenamento de Blobs do Azure para a Instância Gerenciada usando o arquivo de backup Wide World Importers – Standard. Esse método exige um tempo de inatividade. 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

Para obter um tutorial usando o DMS (Serviço de Migração de Banco de Dados do Azure) para a migração, confira [Migração de Instância Gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md). Para conferir uma discussão sobre vários métodos de migração, consulte [Migração da instância do SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido:
- Usa como ponto de partida os recursos criados neste início rápido: [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
- Exige a versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) no computador cliente local
- Requer conectividade com sua Instância Gerenciada por meio do SQL Server Management Studio. Consulte estes inícios rápidos para conhecer as opções de conectividade:
  - [Conectar-se a uma Instância Gerenciada do Banco de Dados SQL do Azure de uma VM do Azure](sql-database-managed-instance-configure-vm.md)
  - [Conectar-se a uma Instância Gerenciada do Banco de Dados SQL do Azure do local usando a conexão ponto a site](sql-database-managed-instance-configure-p2s.md).
- Usa uma conta de armazenamento de blobs do Azure pré-configurada que contém o arquivo de backup Wide World Importers – Standard (baixado de https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak).

> [!NOTE]
> Para obter mais informações sobre o backup e a restauração de um banco de dados do SQL Server usando o armazenamento de blobs do Azure e uma Assinatura de Acesso Compartilhado (SAS), consulte [Backup do SQL Server na URL](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Restaurar o banco de dados da Wide World Importers de um arquivo de backup

Com o SSMS, use as etapas a seguir para restaurar o banco de dados da Wide World Importers para sua Instância Gerenciada do arquivo de backup.

1. Abra o SSMS (SQL Server Management Studio) e conecte-o à sua Instância Gerenciada.
2. No SSMS, abra uma nova janela de consulta.
3. Use o seguinte script para criar uma credencial na Instância Gerenciada usando a conta de armazenamento pré-configurada e a chave SAS.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![criar credencial](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > Sempre remova o **?** à esquerda da chave SAS gerada.
  
3. Use o script a seguir para verificar a credencial de SAS e a validade do backup, fornecendo a URL para o contêiner com o arquivo de backup:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![file list](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. Use o script a seguir para restaurar o banco de dados do Wide World Importers de um arquivo de backup, fornecendo a URL para o contêiner com o arquivo de backup:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![restaurar](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. Para acompanhar o status de restauração, execute a consulta abaixo em uma nova sessão de consulta:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. Quando a restauração for concluída, exiba-a no Pesquisador de Objetos. 

## <a name="next-steps"></a>Próximas etapas

- Para solucionar problemas do backup para a URL, consulte [Práticas recomendadas e solução de problemas de backup do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada](sql-database-managed-instance-connect-app.md).
- Para consultar usando uma de suas ferramentas ou linguagens preferidas, consulte [conectar-se e consultar](sql-database-connect-query.md).
