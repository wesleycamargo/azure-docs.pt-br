<properties
	pageTitle="Habilitar o Banco de Dados de Stretch para um banco de dados | Microsoft Azure"
	description="Saiba como configurar um banco de dados para o Banco de Dados de Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Habilitar o Banco de Dados de Stretch para um banco de dados

Para configurar um banco de dados para o Banco de Dados de Stretch, escolha **Tarefas | Stretch | Habilitar** para um banco de dados no SQL Server Management Studio para abrir o assistente **Habilitar Banco de Dados para Stretch**. Você também pode usar o Transact-SQL para habilitar o Banco de Dados de Stretch para um banco de dados.

Se você selecionar **Tarefas | Stretch | Habilitar** para uma tabela e ainda não tiver habilitado o banco de dados para o Banco de Dados de Stretch, o assistente irá configurar o banco de dados para o Banco de Dados de Stretch e permitirá que você configure as tabelas como parte do processo. Siga as etapas neste tópico em vez das etapas em [Habilitar Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-database.md).

A habilitação do Banco de Dados de Stretch em um banco de dados ou tabela exige permissões db\_owner. Habilitar o Banco de Dados de Stretch em um banco de dados também exige as permissões CONTROLAR BANCO DE DADOS.

## Antes de começar

-   Antes de configurar um banco de dados do Stretch, recomendamos que você execute o Supervisor do Banco de Dados de Stretch para identificar os bancos de dados e as tabelas qualificados para o Stretch. O Supervisor do Banco de Dados do Stretch também identifica os problemas de bloqueio. Para obter mais informações, consulte [Identificar bancos de dados e tabelas para o Banco de Dados de Stretch](sql-server-stretch-database-identify-databases.md).

-   Consulte [Limitações do Stretch Database](sql-server-stretch-database-limitations.md).

-   O Banco de Dados de Stretch migra os dados para o Azure. Portanto, você precisa ter uma conta do Azure e uma assinatura para a cobrança. Para obter uma conta do Azure, [clique aqui](http://azure.microsoft.com/pricing/free-trial/).

-   Tenha as informações necessárias para criar um novo banco de dados remoto ou selecionar um banco de dados remoto existente e criar uma regra de firewall que permita ao servidor local comunicar-se com o servidor remoto.

## <a name="EnableTSQLServer"></a>Pré-requisito: permissão para habilitar o Banco de Dados de Stretch no servidor
Antes de habilitar o Banco de Dados de Stretch em um banco de dados ou uma tabela, você precisará habilitá-lo no servidor local. Esta operação requer as permissões sysadmin ou serveradmin.

-   Se você tiver as permissões administrativas necessárias, o assistente **Habilitar Banco de Dados para Stretch** configurará o servidor para Stretch.

-   Se você não tiver as permissões necessárias, um administrador deverá habilitar a opção manualmente executando **sp\_configure** antes de você executar o assistente ou um administrador precisará executar o assistente.

Para habilitar manualmente o Banco de Dados de Stretch no servidor, execute **sp\_configure** e ative a opção **arquivo de dados remoto**. O exemplo a seguir habilita a opção **arquivo de dados remoto** definindo seu valor como 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
Para obter mais informações, consulte [Configurar a Opção de Configuração do Servidor do arquivo de dados remoto](https://msdn.microsoft.com/library/mt143175.aspx) e [sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Usar o assistente para habilitar o Banco de Dados de Stretch em um banco de dados
Para obter informações sobre o Assistente para Habilitar Banco de Dados para Stretch, incluindo as informações que você precisa inserir e as escolhas que precisa fazer, consulte [Get started by running the Enable Database for Stretch Wizard (Comece executando o assistente para habilitar o banco de dados para Stretch)](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Usar o Transact-SQL para habilitar o Banco de Dados de Stretch em um banco de dados
Antes de habilitar o Banco de Dados de Stretch em tabelas individuais, você precisa habilitá-lo no banco de dados.

A habilitação do Banco de Dados de Stretch em um banco de dados ou tabela exige permissões db\_owner. Habilitar o Banco de Dados de Stretch em um banco de dados também exige as permissões CONTROLAR BANCO DE DADOS.

1.  Antes de começar, escolha um servidor existente do Azure para os dados que o Banco de Dados de Stretch migra ou crie um novo servidor.

2.  No servidor do Azure, crie uma regra de firewall com o endereço IP (ou o intervalo de endereços IP) do SQL Server que permita ao SQL Server comunicar-se com o servidor remoto.

3.  Para configurar um banco de dados do SQL Server para o Banco de Dados de Stretch, o banco de dados deve ter uma chave mestra do banco de dados. A chave mestra do banco de dados protege as credenciais que o banco de Dados de Stretch usa para se conectar ao banco de dados remoto. Para criar manualmente a chave mestra do banco de dados, consulte [CRIAR CHAVE MESTRA (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [Criar uma Chave Mestra do Banco de Dados](https://msdn.microsoft.com/library/aa337551.aspx).

    ```tsql
    USE <database>
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>'
    ```

4.  Quando você configura um banco de dados para o Banco de Dados de Stretch, precisa fornecer uma credencial para o Banco de Dados de Stretch usar para a comunicação entre o SQL Server local e o servidor remoto do Azure. Você tem duas opções.

    -   Pode fornecer uma credencial do administrador.

        -   Se você habilitar o Banco de Dados de Stretch executando o assistente, poderá criar a credencial nesse momento.

        -   Se habilitar o Banco de Dados de Stretch executando **ALTERAR BANCO DE DADOS**, precisará criar a credencial manualmente antes de ativar o Banco de Dados de Stretch.

        Para criar a credencial manualmente, consulte [CRIAR CREDENCIAL COM ESCOPO DO BANCO DE DADOS (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Criar a credencial requer as permissões ALTERAR QUALQUER CREDENCIAL.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>'
        GO
        ```

    -   Você poderá usar uma conta de serviço federado para o SQL Server para se comunicar com o servidor do Azure remoto quando as seguintes condições forem verdadeiras.

        -   A conta de serviço sob a qual a instância do SQL Server está em execução é uma conta de domínio.

        -   A conta de domínio pertence a um domínio ao qual Active Directory é federado com o Azure Active Directory.

        -   O servidor do Azure remoto é configurado para oferecer suporte à autenticação do Azure Active Directory.

        -   A conta de serviço sob a qual a instância do SQL Server está em execução deve ser configurada como uma conta dbmanager ou sysadmin no servidor remoto do Azure.

5.  Para configurar um banco de dados para o Banco de Dados de Stretch, execute o comando ALTER DATABASE.

    1.  Para o argumento SERVER, forneça o nome de um servidor existente do Azure, incluindo a `.database.windows.net` parte do nome, por exemplo, `MyStretchDatabaseServer.database.windows.net`.

    2.  Forneça uma credencial de administrador existente com o argumento CREDENTIAL ou especifique FEDERATED\_SERVICE\_ACCOUNT = ON. O exemplo a seguir fornece uma credencial existente.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO;
    ```

## Próximas etapas
Habilitar outras tabelas para o Banco de Dados de Stretch. Monitorar a migração de dados e gerenciar as tabelas e bancos de dados habilitados para o Stretch.

-   [Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md) para habilitar outras tabelas.

-   [Monitorar o Banco de Dados de Stretch](sql-server-stretch-database-monitor.md) para ver o status da migração dos dados.

-   [Pausar e retomar o Banco de Dados de Stretch](sql-server-stretch-database-pause.md)

-   [Gerenciar e solucionar problemas do Banco de Dados de Stretch](sql-server-stretch-database-manage.md)

-   [Fazer backup de bancos de dados habilitados para o Stretch](sql-server-stretch-database-backup.md)

## Consulte também

[Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md)

[Opções ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0615_2016-->