---
title: Replicação com a Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre o uso da Replicação do SQL Server com a Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 25d13ba53eb5a8b411a557b5eaf05d278faa3733
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869305"
---
# <a name="replication-with-sql-database-managed-instance"></a>Replicação com a Instância Gerenciada do Banco de Dados SQL

A replicação está disponível para versão prévia pública na [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). Uma Instância Gerenciada pode hospedar bancos de dados publicadores, distribuidores e assinantes.

## <a name="common-configurations"></a>Configurações comuns

De modo geral, o publicador e o distribuidor devem, ambos, estar na nuvem ou ser locais. Há suporte para as seguintes configurações:

- **Publicador com distribuidor local na instância gerenciada**

   ![Replication-with-azure-sql-db-single-managed-instance-publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Os bancos de dados publicador e distribuidor são configurados em uma única instância gerenciada.

- **Publicador com distribuidor remoto na instância gerenciada**

   ![Replication-with-azure-sql-db-separate-managed-instances-publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   O publicador e o distribuidor são configurados em duas instâncias gerenciadas. Nesta configuração:

  - As duas instâncias gerenciadas estão na mesma vNet.

  - As duas instâncias gerenciadas estão no mesmo local.

- **Publicador e distribuidor locais com assinante na instância gerenciada**

   ![Replication-from-on-premises-to-azure-sql-db-subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   Nesta configuração, um Banco de Dados SQL do Azure é um assinante. Essa configuração dá suporte à migração do local para o Azure. Na função de assinante, o Banco de Dados SQL não requer a Instância Gerenciada. No entanto, você pode usar uma Instância Gerenciada do Banco de Dados SQL como uma etapa na migração do local para o Azure. Para obter mais informações sobre assinantes no Banco de Dados SQL do Azure, confira [Replicação no Banco de Dados SQL](replication-to-sql-database.md).

## <a name="requirements"></a>Requisitos

A configuração com publicador e distribuidor no Banco de Dados SQL do Azure requer:

- Instância Gerenciada do Banco de Dados SQL do Azure.

   >[!NOTE]
   >Bancos de Dados SQL do Azure não configurados com a Instância Gerenciada podem ser apenas assinantes.

- Todas as instâncias do SQL Server precisam estar na mesma vNet.

- A conectividade usa Autenticação SQL entre os participantes da replicação.

- Um compartilhamento da Conta de Armazenamento do Azure para o diretório de trabalho de replicação.

## <a name="features"></a>Recursos

Suporta:

- Combinação de replicação transacional e de instantâneo de instâncias locais e da Instância Gerenciada do Banco de Dados SQL do Azure.

- Os assinantes podem ser locais, bancos de dados únicos no Banco de Dados SQL do Azure ou bancos de dados em pool nos pools elásticos do Banco de Dados SQL do Azure.

- Replicação unidirecional ou bidirecional

## <a name="configure-publishing-and-distribution-example"></a>Exemplo de configuração de publicação e distribuição

1. [Crie uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-create-tutorial-portal.md) no portal.
2. [Crie uma Conta de Armazenamento do Azure](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho.

   Certifique-se de copiar as chaves de armazenamento. Consulte [Exibir e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys
).
3. Crie um banco de dados para o publicador.

   Nos scripts de exemplo abaixo, substitua `<Publishing_DB>` pelo nome do banco de dados.

4. Crie um usuário do banco de dados com Autenticação SQL para o distribuidor. Consulte [Criar usuários de banco de dados](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Use uma senha segura.

   Nos scripts de exemplo abaixo, use `<SQL_USER>` e `<PASSWORD>` com o usuário e a senha do banco de dados dessa Conta do SQL Server.

5. [Conecte-se à Instância Gerenciada do Banco de Dados SQL](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

6. Execute a seguinte consulta para adicionar o distribuidor e o banco de dados de distribuição.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Para configurar um publicador para usar um banco de dados de distribuição especificado, atualize e execute a consulta a seguir.

   Substitua `<SQL_USER>` e `<PASSWORD>` pela Conta do SQL Server e pela senha.

   Substitua `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` pelo valor de sua conta de armazenamento.  

   Substitua `<STORAGE_CONNECTION_STRING>` pela cadeia de conexão na guia **Chaves de acesso** de sua conta de Armazenamento do Microsoft Azure.

   Após atualizar a consulta a seguir, execute-a.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Configure o publicador para replicação.

    Na consulta a seguir, substitua `<Publishing_DB>` pelo nome do banco de dados publicador.

    Substitua `<Publication_Name>` pelo nome de sua publicação.

    Substitua `<SQL_USER>` e `<PASSWORD>` pela Conta do SQL Server e pela senha.

    Após atualizar a consulta, execute-a para criar a publicação.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Adicione o artigo, a assinatura e o agente da assinatura push.

   Para adicionar esses objetos, atualize o script a seguir.

   - Substitua `<Object_Name>` pelo nome do objeto da publicação.
   - Substitua `<Object_Schema>` pelo nome do esquema de origem.
   - Substitua os outros parâmetros entre colchetes angulares `<>` para corresponder aos valores nos scripts anteriores.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>Limitações

Não há suporte para os seguintes recursos:

- Assinaturas atualizáveis

- Replicação geográfica ativa

## <a name="see-also"></a>Veja também

- [O que é uma Instância Gerenciada?](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
