---
title: Restaurar um backup na Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure usando SSMS.
keywords: tutorial banco de dados sql, instância gerenciada do banco de dados sql, restaurar um backup
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure

Este tutorial demonstra como restaurar um backup de um banco de dados armazenado no Armazenamento de Blobs do Azure para a Instância Gerenciada usando Wide World Importers - arquivo de backup padrão. Esse método exige um tempo de inatividade. Para obter um tutorial usando o DMS (Serviço de Migração de Banco de Dados do Azure) para a migração, confira [Migração de Instância Gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md). Para conferir uma discussão sobre vários métodos de migração, consulte [Migração de instância do SQL Server para Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Baixar arquivo Wide World Importers - backup padrão
> * Criar conta de armazenamento do Azure e carregar o arquivo de backup
> * Restaurar o banco de dados da Wide World Importers de um arquivo de backup

## <a name="prerequisites"></a>pré-requisitos

Este tutorial usa como ponto de partida os recursos criados neste tutorial: [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-create-tutorial-portal.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Baixar arquivo Wide World Importers - backup padrão

Use as etapas a seguir para baixar o arquivo Wide World Importers - backup padrão.

Usando o Internet Explorer, insira https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak na caixa de endereço de URL e, ao receber a solicitação, clique em **Salvar** para salvar esse arquivo na pasta **Downloads**.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Criar conta de armazenamento do Azure e carregar o arquivo de backup

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize **Armazenamento** e clique em **Conta de Armazenamento** para abrir o formulário da conta de armazenamento.

   ![do Azure](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Preencha o formulário de conta de armazenamento com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Modelo de implantação**|Modelo de recursos||
   |**Tipo de conta**|Armazenamento de blob||
   |**Desempenho**|Standard ou Premium|Unidades magnéticas ou SSDs|
   |**Replicação**|Armazenamento com redundância local||
   |**Camada de acesso (padrão)|Inativo ou ativo||
   |**Transferência segura é necessária**|Desabilitado||
   |**Assinatura**|Sua assinatura|Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|O grupo de recursos criado anteriormente|| 
   |**Localidade**|O local selecionado anteriormente||
   |**Redes virtuais**|Desabilitado||

4. Clique em **Criar**.

   ![storage account details](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Depois que a implantação da conta de armazenamento tiver êxito, abra sua nova conta de armazenamento.
6. Em **Configurações**, clique em **Assinatura de Acesso Compartilhado** para abrir o formulário de SAS (assinatura de acesso compartilhado).

   ![sas form](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. No formulário SAS, modifique os valores padrão conforme desejado. Observe que a data/hora de validade é, por padrão, de apenas 8 horas.
8. Clique em **Gerar SAS**.

   ![sas form completed](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Copie e salve o **token SAS** e a **URL SAS do servidor de blobs**.
10. Em **Configurações**, clique em **Contêineres**.

    ![contêineres](./media/sql-database-managed-instance-tutorial/containers.png)

11. Clique em **+ Contêiner** para criar um contêiner e armazenar o arquivo de backup.
12. Preencha o formulário de contêiner com as informações solicitadas, usando as informações na tabela abaixo:

    | Configuração| Valor sugerido | Descrição |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nível de acesso público**|Privado (sem acesso anônimo)||

    ![container detail](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Clique em **OK**.
14. Depois que o contêiner tiver sido criado, abra-o.

    ![contêiner](./media/sql-database-managed-instance-tutorial/container.png)

15. Clique em **Propriedades do contêiner** e copie a URL para o contêiner.

    ![container URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Clique em **Carregar** para abrir a o formulário **Carregar blob**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Procure a pasta de download e selecione o arquivo **WideWorldIimporters-Standard.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Clique em **Carregar**.
19. Não continue até que o upload seja concluído.

    ![upload complete](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Restaurar o banco de dados da Wide World Importers de um arquivo de backup

Com o SSMS, use as etapas a seguir para restaurar o banco de dados da Wide World Importers para sua Instância Gerenciada do arquivo de backup.

1. No SSMS, abra uma nova janela de consulta.
2. Use o script a seguir para criar uma credencial SAS, fornecendo a URL para o contêiner da conta de armazenamento e a chave SAS, conforme indicado.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credencial](./media/sql-database-managed-instance-tutorial/credential.png)

3. Use o script a seguir para criar e verificar a credencial de SAS e a validade do backup, fornecendo a URL para o contêiner com o arquivo de backup:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![file list](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Use o script a seguir para restaurar o banco de dados do Adventure Works 2012 de um arquivo de backup, fornecendo a URL para o contêiner com o arquivo de backup:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![restore executing](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Para acompanhar o status de restauração, execute a consulta abaixo em uma nova sessão de consulta:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![restore percent complete](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Quando a restauração for concluída, exiba-a no Pesquisador de Objetos. 

    ![restore complete](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a restaurar um backup de um banco de dados armazenado no Armazenamento de Blobs do Azure para a Instância Gerenciada usando Wide World Importers - arquivo de backup padrão. Você aprendeu como: 

> [!div class="checklist"]
> * Baixar arquivo Wide World Importers - backup padrão
> * Criar conta de armazenamento do Azure e carregar o arquivo de backup
> * Restaurar o banco de dados da Wide World Importers de um arquivo de backup

Avance para o próximo tutorial para aprender a migrar o SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure usando DMS.

> [!div class="nextstepaction"]
>[Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure usando DMS](../dms/tutorial-sql-server-to-managed-instance.md)