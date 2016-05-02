<properties
   pageTitle="Gerenciamento de segurança de banco de dados SQL - segurança de logon | Microsoft Azure"
   description="Saiba mais sobre o gerenciamento de segurança de banco de dados SQL, especialmente sobre como gerenciar o acesso ao banco de dados e a segurança de logon por meio da conta de entidade de segurança no nível do servidor."
   keywords="segurança do banco de dados SQL, gerenciamento de segurança de banco de dados, segurança de logon, segurança de banco de dados, acesso ao banco de dados"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="03/22/2016"
   ms.author="rickbyh"/>

# Segurança de banco de dados SQL: gerenciar o acesso ao banco de dados e a segurança de logon  

Saiba mais sobre o gerenciamento de segurança de banco de dados SQL, especialmente sobre como gerenciar o acesso ao banco de dados e a segurança de logon por meio da conta de entidade de segurança no nível do servidor. Entenda algumas diferenças e semelhanças nas opções de segurança de logon entre um banco de dados SQL e um SQL Server local. Consulte [Azure SQL Database Tutorial: Get Started with Azure SQL Database Security (Tutorial do Banco de Dados SQL do Azure: introdução à segurança do Banco de Dados SQL do Azure)](sql-database-get-started-security.md) para obter um tutorial rápido.

## Provisionamento de banco de dados e logon da entidade de segurança no nível do servidor

No Banco de dados SQL do Microsoft Azure, quando você se inscreve para o serviço, o processo de provisionamento cria um servidor SQL do Azure, um banco de dados denominado **mestre**, e um logon que é a entidade no nível de servidor de seu servidor de Banco de Dados SQL do Azure. Esse logon é semelhante à entidade no nível de servidor (**sa**) para uma instância local do SQL Server.

A conta da entidade no nível de servidor do Banco de Dados SQL do Azure sempre tem permissão para gerenciar toda a segurança no nível de servidor e nível de banco de dados. Este tópico descreve como você pode usar a entidade no nível de servidor e outras contas para gerenciar logons e bancos de dados no Banco de dados SQL.

Os usuários do Azure que estiverem acessando o Banco de Dados SQL por meio do RBAC (Controle de Acesso Baseado em Função) do Azure e a API REST do Azure Resource Manager recebem permissões de suas Funções no Azure. Essas funções fornecem acesso às operações de plano de gerenciamento, mas não para as operações de plano de dados. Essas operações de plano de gerenciamento incluem a capacidade de ler várias propriedades e elementos de esquema no Banco de Dados SQL. E permite criar, excluir e configurar alguns recursos de nível de servidor relacionados ao Banco de Dados SQL. Muitas dessas operações de plano de gerenciamento são os itens que você pode ver e configurar ao usar o portal do Azure. Ao usar as funções RBAC, as ações dos membros da função do Azure dentro do banco de dados (como tabelas de lista) são executadas para eles pelo Mecanismo de Banco de Dados, para que eles não sejam afetados pelo sistema de permissão standard do SQL Server de instruções GRANT/REVOKE/DENY. As funções RBAC não incluem a capacidade de ler ou alterar dados, porque essas são as operações de plano de dados. Para obter mais informações, consulte [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).

> [AZURE.IMPORTANT] O Banco de dados SQL V12 permite que os usuários se autentifiquem no banco de dados usando usuários do banco de dados independente. Os usuários do banco de dados independente não exigem logons. Isso torna os bancos de dados mais portáteis, mas reduz a capacidade de controlar o acesso ao banco de dados da entidade no nível de servidor. A habilitação de usuários do banco de dados independente tem impactos de segurança importantes. Para obter mais informações, consulte [Usuários do banco de dados independente - Tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx), [Bancos de dados independentes](https://technet.microsoft.com/library/ff929071.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) e [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md).

## Visão geral do gerenciamento de segurança do banco de dados SQL

O gerenciamento de segurança de banco de dados SQL é semelhante ao gerenciamento de segurança para uma instância local do SQL Server. O gerenciamento da segurança no nível de banco de dados é quase idêntico, com diferenças apenas nos parâmetros disponíveis. Como os Bancos de dados SQL podem ser dimensionados para um ou mais computadores físicos, o Banco de dados SQL do Azure usa uma estratégia diferente para a administração no nível de servidor. A tabela a seguir resume como o gerenciamento de segurança de banco de dados para um SQL Server local é diferente do banco de dados SQL do Azure.

| Ponto de diferença | SQL Server local | Banco de Dados SQL do Azure |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Onde você gerencia a segurança no nível de servidor | Na pasta **Segurança** no Gerenciador de Objetos do SQL Server Management Studio | No banco de dados **mestre** e por meio do portal do Azure |
| Autenticação do Windows | Identidades do Active Directory | Identidades do Active Directory do Azure |
| Função de segurança no nível de servidor para criar logons | Função de servidor fixa **securityadmin** | Função de banco de dados **loginmanager** no banco de dados **mestre** |
| Comandos para gerenciar logons | CREATE LOGIN, ALTER LOGIN, DROP LOGIN | CREATE LOGIN, ALTER LOGIN, DROP LOGIN (existem algumas limitações de parâmetro e você deve estar conectado ao banco de dados **mestre**). |
| Modo de exibição que mostra todos os logons | sys.server\_principals | sys.sql\_logins (você deve estar conectado ao banco de dados **mestre**).|
| Função no nível de servidor para a criação de bancos de dados | Função de banco de dados fixa **dbcreator** | Função de banco de dados **dbmanager** no banco de dados **mestre** |
| Comando para criar um banco de dados | CREATE DATABASE | CREATE DATABASE (Existem algumas limitações de parâmetro e você deve estar conectado ao banco de dados **mestre**) |
| Modo de exibição que lista todos os bancos de dados | sys.databases | sys.databases (Você deve estar conectado ao banco de dados **mestre**.) |

## Administração no nível de servidor e banco de dados mestre

Seu servidor de Banco de Dados SQL do Azure é uma abstração que define um agrupamento de bancos de dados. Bancos de dados associados com o servidor de banco de dados SQL do Azure podem residir em computadores físicos separados no data center da Microsoft. Realize a administração no nível de servidor para todos eles usando um banco de dados individual denominado **mestre**.

O banco de dados **mestre** controla os logons e quais logons têm permissão para criar bancos de dados ou outros logons. Você deverá estar conectado ao banco de dados **mestre** sempre que criar, alterar ou remover logons ou bancos de dados. O banco de dados **mestre** também tem os modos de exibição ``sys.sql_logins`` e ``sys.databases`` que você pode usar para exibir logons e bancos de dados.

> [AZURE.NOTE] O comando ``USE`` não oferece suporte à alternância entre bancos de dados. Estabelecer uma conexão diretamente com o banco de dados de destino.

Você pode gerenciar a segurança no nível de banco de dados para usuários e objetos no Banco de dados SQL do Azure da mesma maneira que faria para uma instância local do SQL Server. Há diferenças apenas nos parâmetros disponíveis para os comandos correspondentes. Para saber mais, consulte [Diretrizes e limitações de segurança do Banco de Dados SQL do Azure](sql-database-security-guidelines.md).

## Gerenciando usuários de banco de dados independentes

Crie o primeiro usuário de banco de dados independente em um banco de dados ao conectar-se ao banco de dados com a entidade de segurança no nível do servidor. Use as instruções ``CREATE USER``, ``ALTER USER`` ou ``DROP USER``. O exemplo a seguir cria um usuário chamado usuário1.

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE] Você deve usar uma senha forte ao criar um usuário de banco de dados independente. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

Os usuários adicionais do banco de dados independente podem ser criados por qualquer usuário com a permissão **ALTER ANY USER**.

O Banco de dados SQL V12 oferece suporte a identidades do Active Directory do Azure como usuários de banco de dados independente, como um recurso de visualização. Para obter mais informações, consulte [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)

A Microsoft recomenda usar usuários de banco de dados independente com o Banco de Dados SQL. Para obter mais informações, consulte [Usuários do banco de dados independente - Tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Gerenciar logons

Gerenciar logons com o logon da entidade no nível de servidor conectando-se ao banco de dados. Você pode usar as instruções ``CREATE LOGIN``, ``ALTER LOGIN`` ou ``DROP LOGIN``. O exemplo a seguir cria um logon chamado **logon1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE] Você deve usar uma senha forte ao criar um logon. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

#### Uso de novos logons

Para se conectar ao Banco de dados SQL do Microsoft Azure usando os logons que você criou, primeiro será necessário conceder permissões no nível de banco de dados a cada logon usando o comando ``CREATE USER``. Para obter mais informações, consulte a seção **Concedendo acesso de banco de dados para um logon** abaixo.

Como algumas ferramentas implementam o protocolo TDS (TDS) de forma diferente, talvez seja necessário acrescentar o nome do servidor do Banco de Dados SQL do Azure ao logon na cadeia de conexão usando a notação ``<login>@<server>``. Nesses casos, separe o logon e o nome do servidor do Banco de Dados SQL do Azure com o símbolo ``@``. Por exemplo, se o nome de logon for **logon1** e o nome totalmente qualificado do seu servidor do Banco de Dados SQL do Azure for **nomedoservidor.database.windows.net**, o parâmetro de nome de usuário da cadeia de conexão deverá ser: ****login1@servername**. Essa restrição impõe limitações no texto que você pode escolher para o nome de logon. Para saber mais, confira [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).

## Concessão de permissões no nível de servidor para um logon

Para que logons diferentes da entidade no nível de servidor gerenciem a segurança no nível de servidor, o Banco de Dados SQL do Azure oferece duas funções de segurança: **loginmanager**, para a criação de logons e **dbmanager** para a criação de bancos de dados. Apenas os usuários no banco de dados **mestre** podem ser adicionados a essas funções de banco de dados.

> [AZURE.NOTE] Para criar logons ou bancos de dados, você deverá estar conectado ao banco de dados **mestre** (que é uma representação lógica do **mestre**).

### A função loginmanager

Assim como a função de servidor fixo **securityadmin** para uma instância local do SQL Server, a função de banco de dados **loginmanager** no Banco de Dados SQL do Azure tem permissão para criar logons. Somente o logon da entidade de segurança no nível de servidor (criado pelo processo de provisionamento) ou os membros da função de banco de dados **loginmanager** podem criar novos logons.

### A função dbmanager

A função **dbmanager** do Banco de Dados SQL do Azure é semelhante à função de servidor fixo **dbcreator** para uma instância local do SQL Server. Somente o logon da entidade de segurança no nível de servidor (criado pelo processo de provisionamento) ou os membros da função de banco de dados **dbmanager** podem criar bancos de dados. Quando um usuário for membro da função **dbmanager** do banco de dados, ele poderá criar um banco de dados com o comando ``CREATE DATABASE`` do Banco de Dados SQL do Azure, mas esse comando deverá ser executado no banco de dados mestre. Para saber mais, confira [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).

### Como atribuir funções no nível de servidor do Banco de dados SQL

Para criar um logon e um usuário associado que possa criar bancos de dados ou outros logons, execute as seguintes etapas:

1. Conecte-se ao banco de dados **mestre** usando as credenciais de logon da entidade de segurança no nível de servidor (criado pelo processo de provisionamento) ou as credenciais de um membro existente da função de banco de dados **loginmanager**.
2. Crie um logon usando o comando ``CREATE LOGIN``. Para saber mais, confira [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).
3. Crie um novo usuário para esse logon no banco de dados mestre usando o comando ``CREATE USER``. Para obter mais informações, consulte [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).
4. Use o procedimento armazenado ``sp_addrolememeber`` para adicionar um novo usuário à função de banco de dados **dbmanager**, à função de banco de dados loginmanager, ou a ambas.

O exemplo de código a seguir mostra como criar um logon chamado **logon1** e um usuário de banco de dados correspondente chamado **logon1Usuário** que é capaz de criar bancos de dados ou outros logons enquanto está conectado ao banco de dados **mestre**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE] Você deve usar uma senha forte ao criar um logon. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

## Concessão de acesso de banco de dados para um logon

Todos os logons devem ser criados no banco de dados **mestre**. Depois que um logon tiver sido criado, você pode criar uma conta de usuário em outro banco de dados para esse logon. O Banco de Dados SQL do Azure também oferece suporte a funções do banco de dados da mesma maneira que uma instância local do SQL Server faz.

Para criar uma conta de usuário em outro banco de dados, supondo que você não tenha criado um logon ou um banco de dados, execute as seguintes etapas:

1. Conecte-se ao banco de dados **mestre** (com um logon que tenha as funções **loginmanager** e **dbmanager**).
2. Crie um novo logon usando o comando ``CREATE LOGIN``. Para saber mais, confira [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). A Autenticação do Windows não é suportada.
3. Crie um novo banco de dados usando o comando ``CREATE DATABASE``. Para saber mais, confira [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).
4. Estabelecer uma conexão com o novo banco de dados (com o logon que criou o banco de dados).
5. Crie um novo usuário no novo banco de dados usando o comando ``CREATE USER``. Para obter mais informações, consulte [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

O exemplo de código a seguir mostra como criar um logon chamado **logon1** e um banco de dados chamado **bancodedados1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE] Você deve usar uma senha forte ao criar um logon. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms161962.aspx).

O exemplo abaixo mostra como criar um usuário de banco de dados chamado **login1User** no banco de dados **database1** que corresponde ao logon **login1**. Para executar o exemplo a seguir, você deve primeiro criar uma nova conexão a database1, usando um logon com a permissão **ALTERAR QUALQUER USUÁRIO** no banco de dados. Qualquer usuário que se conecte como membro da função **db\_owner** terá essa permissão, como o logon que criou o banco de dados.

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Esse modelo de permissão no nível de banco de dados no Banco de Dados SQL do Azure é o mesmo de uma instância local do SQL Server. Para obter informações, consulte os tópicos a seguir nas referências dos Manuais Online do SQL Server.

- [Tópicos de instrução para Gerenciamento de logons, usuários e esquemas](https://msdn.microsoft.com/library/aa337552.aspx)
- [Lição 2: Configurando permissões em objetos do banco de dados](https://msdn.microsoft.com/library/ms365345.aspx)

> [AZURE.NOTE] As instruções do Transact-SQL relacionadas a segurança no Banco de Dados SQL do Azure podem diferir ligeiramente nos parâmetros disponíveis. Para saber mais, confira a sintaxe dos Manuais Online para obter instruções específicas.

## Exibição de logons e bancos de dados


Para exibir logons e bancos de dados em seu servidor do Banco de Dados SQL do Azure, use as exibições ``sys.sql_logins`` e ``sys.databases`` do banco de dados mestre, respectivamente. O exemplo a seguir mostra como exibir uma lista de todos os logons e bancos de dados em seu servidor de Banco de dados SQL do Azure.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases;
```

## Consulte também

[Azure SQL Database Tutorial: Get Started with Azure SQL Database Security (Tutorial do Banco de Dados SQL do Azure: introdução à segurança do Banco de Dados SQL do Azure)](sql-database-get-started-security.md) [Limitações e diretrizes de segurança de Banco de Dados SQL do Azure](sql-database-security-guidelines.md) [Conectar-se ao Banco de Dados SQL Usando a Autenticação do Azure Active Directory](sql-database-aad-authentication.md)

<!---HONumber=AcomDC_0420_2016-->