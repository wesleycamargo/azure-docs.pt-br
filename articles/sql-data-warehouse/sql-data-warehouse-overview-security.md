<properties
   pageTitle="Proteger um banco de dados no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para proteger um banco de dados no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Proteger um banco de dados no SQL Data Warehouse

Este artigo apresenta os conceitos básicos da proteção do banco de dados do SQL Data Warehouse do Azure. Especificamente, este artigo apresentará a você recursos para limitar acesso, proteger dados e monitorar atividades em um banco de dados.

## Segurança da conexão

A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo servidor e pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não foram incluídos explicitamente na lista de permissões. Para permitir que seu aplicativo ou o endereço IP público do computador cliente tente se conectar a um novo banco de dados, primeiramente você deve criar uma regra de firewall no nível de servidor usando o Portal Clássico do Azure, a API REST ou o PowerShell. Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor. Para obter mais informações, consulte [Firewall do Banco de Dados SQL do Azure][].


## Autenticação

A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. Atualmente, o SQL Data Warehouse oferece suporte à Autenticação SQL com um nome de usuário e uma senha.

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo".

No entanto, como uma melhor prática, os usuários de sua organização devem usar uma conta diferente para a autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. Para criar um usuário de banco de dados com base no logon do servidor:

Primeiro, conecte-se ao banco de dados mestre no servidor com o logon de administrador de servidor e crie um novo logon de servidor.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

Em seguida, conecte-se ao banco de dados do SQL Data Warehouse com seu logon de administrador de servidor e crie um usuário de banco de dados com base no logon de servidor que você acabou de criar.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

Para obter mais informações sobre como fazer a autenticação em um Banco de Dados SQL, consulte [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure][].


## Autorização

A Autorização refere-se ao que você pode fazer em um banco de dados do SQL Data Warehouse, e isso é controlado pelas associações e permissões da função da sua conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. O SQL Data Warehouse do Azure facilita o gerenciamento deles com funções no T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db\_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Existem maneiras de limitar ainda mais o que um usuário pode fazer com o Banco de Dados SQL do Azure:

- [Funções de banco de dados][] diferentes de db\_datareader e db\_datawriter podem ser usadas para criar contas de usuário de aplicativo mais potentes ou contas de gerenciamento menos potentes.
- [Permissões][] granulares permitem controlar quais operações você pode fazer em colunas, tabelas, exibições, procedimentos e outros objetos individuais no banco de dados.
- [Procedimentos armazenados][] podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O gerenciamento de bancos de dados e servidores lógicos pelo Portal Clássico do Azure ou usando a API do Gerenciador de Recursos do Azure é controlado pelas atribuições de função da sua conta de usuário. Para saber mais sobre esse tópico, confira [Controle de acesso baseado em função no portal do Azure][].

## Criptografia

O SQL Data Warehouse do Azure pode ajudar a proteger seus dados criptografando-os quando estiverem “em repouso” ou armazenados em arquivos e backups de banco de dados usando a [Transparent Data Encryption][]. Para criptografar o banco de dados, conecte-se ao banco de dados mestre em seu servidor e execute:


```sql

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

Você também pode habilitar a Transparent Data Encryption por meio das configurações de banco de dados no [Portal Clássico do Azure][].

## Auditoria

A Auditoria e o rastreamento dos eventos de banco de dados podem ajudar você a manter a conformidade normativa e a identificar atividades suspeitas. A Auditoria do SQL Data Warehouse permite registrar eventos no banco de dados em um log de auditoria na sua conta de armazenamento do Azure. A Auditoria do SQL Data Warehouse também se integra ao Microsoft Power BI para facilitar análises e relatórios detalhados. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL][].

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Firewall do Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[Funções de banco de dados]: https://msdn.microsoft.com/library/ms189121.aspx
[Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissões]: https://msdn.microsoft.com/library/ms191291.aspx
[Procedimentos armazenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?LinkId=526242
[Introdução à Auditoria do Banco de Dados SQL]: sql-database-auditing-get-started.md
[Portal Clássico do Azure]: https://portal.azure.com/

<!--Other Web references-->
[Controle de acesso baseado em função no portal do Azure]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!-----------HONumber=AcomDC_0330_2016-->