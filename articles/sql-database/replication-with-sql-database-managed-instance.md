---
title: Configurar a replicação na Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre como configurar a replicação transacional na Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/16/2019
ms.openlocfilehash: 568b239cf41c802cc5d25b638f6d1501f58eccdf
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360081"
---
# <a name="configure-replication-in-azure-sql-database-managed-instance"></a>Configurar a replicação na Instância Gerenciada do Banco de Dados SQL do Azure

A replicação transacional permite replicar dados de bancos de dados do SQL Server ou da Instância Gerenciada do Banco de Dados SQL do Azure para a Instância Gerenciada ou enviar por push as alterações feitas nos bancos de dados na Instância Gerenciada para outro SQL Server, outro banco de dados individual do Azure ou outra Instância Gerenciada. A replicação está em versão prévia pública na [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). Uma Instância Gerenciada pode hospedar bancos de dados publicadores, distribuidores e assinantes. Confira [Configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para obter as configurações disponíveis.

## <a name="requirements"></a>Requisitos

A configuração com publicador e distribuidor no Banco de Dados SQL do Azure requer:

- Uma Instância Gerenciada do Banco de Dados SQL do Azure que não esteja na configuração de recuperação de desastre geográfico.

   >[!NOTE]
   >Bancos de Dados SQL do Azure não configurados com a Instância Gerenciada podem ser apenas assinantes.

- Todas as instâncias do SQL Server precisam estar na mesma vNet.

- A conectividade usa Autenticação SQL entre os participantes da replicação.

- Um compartilhamento da Conta de Armazenamento do Azure para o diretório de trabalho de replicação.

- A porta 445 (TCP de saída) precisa ser aberta nas regras de segurança de sub-rede da Instância Gerenciada para acessar o compartilhamento de arquivos do Azure

## <a name="features"></a>Recursos

Suporta:

- Combinação de replicação transacional e de instantâneo de instâncias locais e da Instância Gerenciada do Banco de Dados SQL do Azure.

- Os assinantes podem ser locais, bancos de dados únicos no Banco de Dados SQL do Azure ou bancos de dados em pool nos pools elásticos do Banco de Dados SQL do Azure.

- Replicação unidirecional ou bidirecional.

Não há suporte para os seguintes recursos:

- Assinaturas atualizáveis.

- Replicação geográfica ativa.

## <a name="configure-publishing-and-distribution-example"></a>Exemplo de configuração de publicação e distribuição

1. [Crie uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-create-tutorial-portal.md) no portal.
2. [Crie uma Conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho.

   Certifique-se de copiar as chaves de armazenamento. Consulte [Exibir e copiar chaves de acesso de armazenamento](../storage/common/storage-account-manage.md#access-keys
).
3. Crie um banco de dados para o publicador.

   Nos scripts de exemplo abaixo, substitua `<Publishing_DB>` pelo nome do banco de dados.

4. Crie um usuário do banco de dados com Autenticação SQL para o distribuidor. Use uma senha segura.

   Nos scripts de exemplo abaixo, use `<SQL_USER>` e `<PASSWORD>` com o usuário e a senha do banco de dados dessa Conta do SQL Server.

5. [Conecte-se à Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

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
   
## <a name="see-also"></a>Veja também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [O que é uma Instância Gerenciada?](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
