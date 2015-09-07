<properties
   pageTitle="Gerenciamento dos bancos de dados e logons no Banco de Dados SQL do Azure | Microsoft Azure"
	description="Como usar a entidade no nível de servidor e outras contas para gerenciar logons e bancos de dados no Banco de dados SQL."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""
	tags=""/>

<tags
   ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/20/2015"
	ms.author="rickbyh"/>

# Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure

No Banco de dados SQL do Microsoft Azure, quando você se inscreve para o serviço, o processo de provisionamento cria um servidor SQL do Azure, um banco de dados denominado **mestre**, e um logon que é a entidade no nível de servidor de seu servidor de Banco de Dados SQL do Azure. Esse logon é semelhante à entidade no nível de servidor, **sa**, para uma instância do SQL Server no seu local.

A conta da entidade no nível de servidor do Banco de Dados SQL do Azure sempre tem permissão para gerenciar toda a segurança no nível de servidor e nível de banco de dados. Este tópico descreve como você pode usar a entidade no nível de servidor e outras contas para gerenciar logons e bancos de dados no Banco de dados SQL.

> [AZURE.IMPORTANT]O Banco de dados SQL V12 permite que os usuários se autentifiquem no banco de dados usando usuários do banco de dados independente. Os usuários do banco de dados independente não exigem logons. Isso torna os bancos de dados mais portáteis, mas reduz a capacidade de controlar o acesso ao banco de dados da entidade no nível de servidor. A habilitação de usuários do banco de dados independente tem impactos de segurança importantes. Para obter mais informações, consulte [Usuários do Banco de dados Independente - Tornado portátil o banco de dados](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx), e [Bancos de dados independentes](https://msdn.microsoft.com/library/ff929071.aspx).

## Visão geral da administração de segurança do Banco de dados SQL

A administração de segurança no Banco de dados SQL é semelhante à administração de segurança para uma instância local do SQL Server. O gerenciamento da segurança no nível de banco de dados é quase idêntico, com diferenças apenas nos parâmetros disponíveis. Como os Bancos de dados SQL podem ser dimensionados para um ou mais computadores físicos, o Banco de dados SQL do Azure usa uma estratégia diferente para a administração no nível de servidor. A tabela a seguir resume como a administração de segurança para um SQL Server local é diferente do Banco de dados SQL do Azure.

| Ponto de diferença | SQL Server local | Banco de Dados SQL do Azure |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Onde você gerencia a segurança no nível de servidor | A pasta Segurança no Gerenciador de objetos do SQL Server Management Studio | O banco de dados mestre e por meio do portal do Azure |
| Função de segurança no nível de servidor para criar logons | função de servidor fixo securityadmin Para obter mais informações, consulte as funções no nível de servidor | função de banco de dados loginmanager no banco de dados mestre |
| Comandos para gerenciar logons | CREATE LOGIN, ALTER LOGIN, DROP LOGIN | CREATE LOGIN, ALTER LOGIN, DROP LOGIN (existem algumas limitações de parâmetro e você deve estar conectado ao banco de dados mestre) |
| Modo de exibição que mostra todos os logons | sys.server\_principals | sys. sql\_logins (Você deve estar conectado ao banco de dados mestre)|
| Função no nível de servidor para a criação de bancos de dados | função de banco de dados fixo dbcreator Para obter mais informações, consulte as funções no nível de servidor | função de banco de dados dbmanager no banco de dados mestre |
| Comando para criar um banco de dados | CREATE DATABASE | CREATE DATABASE (existem algumas limitações de parâmetro e você deve estar conectado ao banco de dados mestre) |
| Modo de exibição que lista todos os bancos de dados | sys.databases | sys.databases (Você deve estar conectado ao banco de dados mestre) |

### Administração no nível de servidor e banco de dados mestre

Seu servidor de Banco de Dados SQL do Azure é uma abstração que define um agrupamento de bancos de dados. Bancos de dados associados com o servidor de banco de dados SQL do Azure podem residir em computadores físicos separados no data center da Microsoft. Realize a administração no nível de servidor para todos os eles usando um banco de dados individual denominado mestre.

O banco de dados mestre controla os logons e quais logons têm permissão para criar bancos de dados ou outros logons. Você deve estar conectado ao banco de dados mestre sempre que você cria, altera ou remove logons ou bancos de dados. O banco de dados **mestre** também tem os modos de exibição ``sys.sql_logins`` e ``sys.databases`` que você pode usar para exibir logons e bancos de dados, respectivamente.

> [AZURE.NOTE]O comando ``USE`` não oferece suporte para alternar entre bancos de dados. Estabelecer uma conexão diretamente com o banco de dados de destino.

Você pode gerenciar a segurança no nível de banco de dados para usuários e objetos no Banco de dados SQL do Microsoft Azure da mesma maneira que faria para uma instância local do SQL Server. Há diferenças apenas nos parâmetros disponíveis para os comandos correspondentes. Para saber mais, consulte [Diretrizes e limitações de segurança do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ff394108.aspx).

### Gerenciar logons

Gerenciar logons com o logon da entidade no nível de servidor conectando-se ao banco de dados. Você pode usar as instruções ``CREATE LOGIN``, ``ALTER LOGIN`` ou ``DROP LOGIN``. O exemplo a seguir cria um logon nomeado** login1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE]Você deve usar uma senha forte ao criar um logon. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

#### Uso de novos logons

Para se conectar ao Banco de dados SQL do Microsoft Azure SQL usando os logons que você criou, você precisa primeiro conceder permissões no nível de banco de dados a cada logon usando o comando ``CREATE USER``. Para obter mais informações, consulte Conceder permissões no nível de banco de dados a um logon.

Como algumas ferramentas implementam o protocolo TDS (TDS) de forma diferente, talvez seja necessário acrescentar o nome do servidor de banco de dados SQL ao logon na cadeia de conexão usando o notação ``<login>@<server>``. Nesses casos, separe o logon e o nome do servidor de banco de dados SQL com o símbolo ``@``. Por exemplo, se o nome de logon for **login1** e é o nome totalmente qualificado do seu servidor de banco de dados SQL do Azure for **servername.database.windows.net**, o parâmetro de nome de usuário da cadeia de conexão deve ser: ****login1@servername**. Essa restrição impõe limitações no texto que você pode escolher para o nome de logon. Para obter mais informações, consulte [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).

## Concessão de permissões no nível de servidor para um logon

Para que logons diferentes da entidade no nível de servidor gerenciem a segurança no nível de servidor, o Banco de dados SQL do Microsoft Azure oferece duas funções de segurança: **loginmanager**, para a criação de logons e **dbmanager** para a criação de bancos de dados. Apenas os usuários no banco de dados **mestre** podem ser adicionados a essas funções de banco de dados.

> [AZURE.NOTE]Para criar logons ou bancos de dados, você deve estar conectado ao banco de dados **mestre** (que é uma representação lógica do **mestre**).

### A função loginmanager

Como a função **securityadmin** do servidor fixo para uma instância local do SQL Server, a função **loginmanager** do banco de dados no Banco de dados SQL do Microsoft Azure tem permissão para criar logons. Somente o logon principal no nível de servidor (criado pelo processo de provisionamento) ou membros da função **loginmanager** do banco de dados pode criar novos logons.

### A função dbmanager

A função **dbmanager** do banco de dados do Banco de Dados SQL do Microsoft Azure é semelhante à função **dbcreator** do servidor fixo para uma instância local do SQL Server. Somente o logon principal no nível de servidor (criado pelo processo de provisionamento) ou membros da função **dbmanager** do banco de dados pode criar bancos de dados. Quando um usuário é membro da função **dbmanager** do banco de dados, ele pode criar um banco de dados com o comando ``CREATE DATABASE`` do Banco de dados SQL do Azure, mas esse comando deve ser executado no banco de dados mestre. Para saber mais, confira [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx).

### Como atribuir funções no nível de servidor do Banco de dados SQL

Para criar um logon e um usuário associado que possa criar bancos de dados ou outros logons, execute as seguintes etapas:

1. Conecte-se ao banco de dados **mestre** usando as credenciais de logon da entidade no nível de servidor (criado pelo processo de provisionamento) ou as credenciais de um membro existente da função **loginmanager** do banco de dados.
2. Crie um logon usando o comando ``CREATE LOGIN``. Para obter mais informações, consulte [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).
3. Criar um novo usuário para esse logon no banco de dados mestre usando o comando ``CREATE USER``. Para obter mais informações, consulte [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).
4. Use o procedimento armazenado ``sp_addrolememeber`` para adicionar um novo usuário à função **dbmanager** do banco de dados, a função loginmanager do banco de dados, ou ambos.

O exemplo de código a seguir mostra como criar um logon denominado **login1**, e um nome de usuário **login1User** correspondente do banco de dados que é capaz de criar bancos de dados ou outros logons enquanto está conectado ao banco de dados **mestre**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE]Você deve usar uma senha forte ao criar um logon. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

## Concessão de acesso de banco de dados para um logon

Todos os logons devem ser criados no banco de dados mestre. Depois que um logon tiver sido criado, você pode criar uma conta de usuário em outro banco de dados para esse logon. O Banco de dados SQL do Microsoft Azure também oferece suporte a funções do banco de dados da mesma maneira que faz uma instância local do SQL Server.

Para criar uma conta de usuário em outro banco de dados, supondo que você não tenha criado um logon ou um banco de dados, execute as seguintes etapas:

1. Conecte-se a banco de dados **mestre** (com um logon que tenha as funções **loginmanager** e **dbmanager**).
2. Crie um logon novo usando o comando ``CREATE LOGIN``. Para obter mais informações, consulte [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). A Autenticação do Windows não é suportada.
3. Crie um banco de dados novo usando o comando ``CREATE DATABASE``. Para saber mais, consulte [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx).
4. Estabelecer uma conexão com o novo banco de dados (com o logon que criou o banco de dados).
5. Crie um usuário novo no banco de dados novo usando o comando ``CREATE USER``. Para obter mais informações, consulte [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

O exemplo de código a seguir mostra como criar um logon denominado **login1** e um banco de dados denominado **database1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE]Você deve usar uma senha forte ao criar um logon. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

O exemplo a seguir mostra como criar um usuário de banco de dados chamado **login1User** no banco de dados **database1** que corresponde ao logon **login1**:

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Esse modelo de permissão no nível de banco de dados no Banco de Dados SQL do Microsoft Azure é igual que em uma instância local do SQL Server. Para obter informações, consulte os tópicos a seguir nas referências dos Manuais Online do SQL Server.

- [Tópicos de instrução para Gerenciamento de logons, usuários e esquemas](https://msdn.microsoft.com/library/aa337552.aspx) 
- [Lição 2: Configurando permissões em objetos do banco de dados](https://msdn.microsoft.com/library/ms365345.aspx) 

> [AZURE.NOTE]As instruções de Transact-SQL relacionadas à segurança no Banco de dados SQL do Microsoft Azure podem diferir ligeiramente nos parâmetros disponíveis. Para saber mais, consulte [Referência de Transact-SQL do Banco de dados SQL do Azure](sql-database-transact-sql-information.md).

## Exibição de logons e bancos de dados

Para exibir logons e bancos de dados em seu servidor de banco de dados SQL, use os modos de visualização ``sys.sql_logins`` e ``sys.databases`` do banco de dados, respectivamente. O exemplo a seguir mostra como exibir uma lista de todos os logons e bancos de dados em seu servidor de Banco de dados SQL do Azure.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases; 
```

## Consulte também

[Limitações e diretrizes de segurança de Banco de Dados SQL do Azure](sql-database-security-guidelines.md)

<!---HONumber=August15_HO9-->