---
title: Habilitar o Stretch Database para um banco de dados | Microsoft Docs
description: Saiba como configurar um banco de dados para o Banco de Dados de Stretch.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 55fc4142-4be9-4664-8ea9-48a5e177838f
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6d3a81c3c919e50c123cef00dfb3e4a5ab23a44


---
# <a name="enable-stretch-database-for-a-database"></a>Habilitar o Banco de Dados de Stretch para um banco de dados
Para configurar um banco de dados existente no Stretch Database, escolha **Tarefas | Stretch | Habilitar** para que um banco de dados no SQL Server Management Studio abra o assistente **Habilitar Stretch Database**. Você também pode usar o Transact\-SQL para habilitar o Stretch Database para um banco de dados.

Se você selecionar **Tarefas | Stretch | Habilitar** para uma tabela individual e ainda não tiver habilitado o banco de dados para o Banco de Dados de Stretch, o assistente irá configurar o banco de dados para o Banco de Dados de Stretch e permitirá que você selecione as tabelas como parte do processo. Siga as etapas neste tópico em vez das etapas em [Habilitar Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-database.md).

A habilitação do Banco de Dados de Stretch em um banco de dados ou tabela exige permissões db\_owner. Habilitar o Banco de Dados de Stretch em um banco de dados também exige as permissões CONTROLAR BANCO DE DADOS.

> [!NOTE]
> Mais tarde, se você desabilitar o Stretch Database, lembre-se de que desabilitar o Stretch Database para uma tabela ou para um banco de dados não exclui o objeto remoto. Se deseja excluir a tabela remota ou o banco de dados remoto, você deve descartá-la(o) usando o Portal de Gerenciamento do Azure. Os objetos remotos continuarão gerando custos no Azure até você excluí-los manualmente.
> 
> 

## <a name="before-you-get-started"></a>Antes de começar
* Antes de configurar um banco de dados do Stretch, recomendamos que você execute o Supervisor do Banco de Dados de Stretch para identificar os bancos de dados e as tabelas qualificados para o Stretch. O Supervisor do Banco de Dados do Stretch também identifica os problemas de bloqueio. Para saber mais, confira [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md)(Identificar bancos de dados e tabelas para o Stretch Database).
* Leia [Limitações do Stretch Database](sql-server-stretch-database-limitations.md).
* O Banco de Dados de Stretch migra os dados para o Azure. Portanto, você precisa ter uma conta do Azure e uma assinatura para a cobrança. Para obter uma conta do Azure, [clique aqui](http://azure.microsoft.com/pricing/free-trial/).
* Tenha as informações de conexão e de logon necessárias para criar um novo servidor do Azure ou para selecionar um servidor existente do Azure.

## <a name="a-nameenabletsqlserveraprerequisite-enable-stretch-database-on-the-server"></a><a name="EnableTSQLServer"></a>Pré-requisito: habilitar o Stretch Database no servidor
Antes de habilitar o Banco de Dados de Stretch em um banco de dados ou uma tabela, você precisará habilitá-lo no servidor local. Esta operação requer as permissões sysadmin ou serveradmin.

* Se você tiver as permissões administrativas necessárias, o assistente **Habilitar Banco de Dados para Stretch** vai configurar o servidor para o Stretch.
* Se você não tiver as permissões necessárias, um administrador deverá habilitar a opção manualmente executando **sp\_configure** antes de você executar o assistente, ou um administrador precisará executar o assistente.

Para habilitar manualmente o Stretch Database no servidor, execute **sp\_configure** e ative a opção **arquivo de dados remoto**. O exemplo a seguir habilita a opção **arquivo de dados remoto** definindo seu valor para 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Para saber mais, confira [Configurar a opção de configuração de servidor do arquivo de dados remoto](https://msdn.microsoft.com/library/mt143175.aspx) e [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="a-namewizardause-the-wizard-to-enable-stretch-database-on-a-database"></a><a name="Wizard"></a>Usar o assistente para habilitar o Stretch Database em um banco de dados
Para obter informações sobre o Assistente para Habilitar Banco de Dados para Stretch, incluindo as informações que você precisa inserir e as escolhas que precisa fazer, confira [Get started by running the Enable Database for Stretch Wizard (Introdução à execução do Assistente para Habilitar Banco de Dados para Stretch)](sql-server-stretch-database-wizard.md).

## <a name="a-nameenabletsqldatabaseause-transact-sql-to-enable-stretch-database-on-a-database"></a><a name="EnableTSQLDatabase"></a>Usar o Transact\-SQL para habilitar o Stretch Database em um banco de dados
Antes de habilitar o Banco de Dados de Stretch em tabelas individuais, você precisa habilitá-lo no banco de dados.

Habilitar o Stretch Database em um banco de dados ou tabela exige permissões db\_owner. Habilitar o Banco de Dados de Stretch em um banco de dados também exige as permissões CONTROLAR BANCO DE DADOS.

1. Antes de começar, escolha um servidor existente do Azure para os dados que o Banco de Dados de Stretch migra ou crie um novo servidor do Azure.
2. No servidor do Azure, crie uma regra de firewall com o endereço IP do SQL Server que permita ao SQL Server comunicar-se com o servidor remoto.
   
   Você pode localizar facilmente os valores necessários e criar a regra de firewall tentando se conectar ao servidor do Azure no Pesquisador de Objetos no SQL Server Management Studio (SSMS). O SSMS ajuda você a criar a regra, abrindo a caixa de diálogo a seguir, que já inclui os valores de endereços IP necessários.
   
   ![Criar uma regra de firewall no SSMS][FirewallRule]
3. Para configurar um banco de dados do SQL Server para o Banco de Dados de Stretch, o banco de dados deve ter uma chave mestra do banco de dados. A chave mestra do banco de dados protege as credenciais que o banco de Dados de Stretch usa para se conectar ao banco de dados remoto. Veja um exemplo que cria uma nova chave mestra de banco de dados.
   
   ```tsql
   USE <database>;
   GO
   
   CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
   GO
   ```
   
   Para saber mais sobre a chave mestra de banco de dados, confira [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [Criar uma chave mestra de banco de dados](https://msdn.microsoft.com/library/aa337551.aspx).
4. Quando você configura um banco de dados para o Banco de Dados de Stretch, precisa fornecer uma credencial para o Banco de Dados de Stretch usar para a comunicação entre o SQL Server local e o servidor remoto do Azure. Você tem duas opções.
   
   * Pode fornecer uma credencial do administrador.
     
     * Se você habilitar o Banco de Dados de Stretch executando o assistente, poderá criar a credencial nesse momento.
     * Se você planeja habilitar o Stretch Database executando **ALTER DATABASE**, será necessário criar a credencial manualmente antes de executar **ALTER DATABASE** para habilitar o Stretch Database.
     
     Veja um exemplo que cria uma nova credencial.
     
     ```tsql
     CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
         WITH IDENTITY = '<identity>' , SECRET = '<secret>';
     GO
     ```
     
     Para saber mais sobre as credenciais, confira [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Criar a credencial requer as permissões ALTERAR QUALQUER CREDENCIAL.
   * Você poderá usar uma conta de serviço federado para o SQL Server para se comunicar com o servidor do Azure remoto quando as seguintes condições forem verdadeiras.
     
     * A conta de serviço sob a qual a instância do SQL Server está em execução é uma conta de domínio.
     * A conta de domínio pertence a um domínio ao qual Active Directory é federado com o Azure Active Directory.
     * O servidor do Azure remoto é configurado para oferecer suporte à autenticação do Azure Active Directory.
     * A conta de serviço sob a qual a instância do SQL Server está em execução deve ser configurada como uma conta dbmanager ou sysadmin no servidor remoto do Azure.
5. Para configurar um banco de dados para o Banco de Dados de Stretch, execute o comando ALTER DATABASE.
   
   1. Para o argumento SERVER, forneça o nome de um servidor existente do Azure, incluindo a parte `.database.windows.net` do nome \-, por exemplo, `MyStretchDatabaseServer.database.windows.net`.
   2. Forneça uma credencial de administrador existente com o argumento CREDENTIAL ou especifique FEDERATED\_SERVICE\_ACCOUNT = ON. O exemplo a seguir fornece uma credencial existente.
   
   ```tsql
   ALTER DATABASE <database name>
       SET REMOTE_DATA_ARCHIVE = ON
           (
               SERVER = '<server_name>',
               CREDENTIAL = <db_scoped_credential_name>
           ) ;
   GO
   ```

## <a name="next-steps"></a>Próximas etapas
* [Habilitar o Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md) para habilitar outras tabelas.
* [Monitorar o Stretch Database](sql-server-stretch-database-monitor.md) para ver o status da migração dos dados.
* [Pausar e retomar o Banco de Dados de Stretch](sql-server-stretch-database-pause.md)
* [Gerenciar e solucionar problemas do Banco de Dados de Stretch](sql-server-stretch-database-manage.md)
* [Fazer backup de bancos de dados habilitados para o Stretch](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Confira também
[Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md)

[Opções ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png



<!--HONumber=Nov16_HO3-->


