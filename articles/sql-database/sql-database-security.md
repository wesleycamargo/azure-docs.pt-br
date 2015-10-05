<properties 
   pageTitle="Visão geral de segurança do Banco de Dados SQL" 
   description="Saiba mais sobre a segurança do Banco de Dados SQL do Azure e SQL Server, incluindo as diferenças entre a nuvem e o SQL Server local quando se trata de autenticação, autorização, segurança de conexão, criptografia e conformidade." 
   services="sql-database" 
   documentationCenter="" 
   authors="tmullaney" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="09/22/2015"
   ms.author="thmullan;jackr"/>


# Protegendo o Banco de Dados SQL

## Visão geral

Este artigo apresenta os conceitos básicos da proteção da camada de dados de um aplicativo usando o Banco de Dados SQL do Azure. Especificamente, este artigo apresentará a você recursos para limitar acesso, proteger dados e monitorar atividades em um banco de dados criado no tutorial [Introdução ao Banco de Dados SQL](sql-database-get-started.md). Para obter uma visão geral completa dos recursos de segurança disponíveis em todas as versões do SQL, confira a [Central de Segurança do mecanismo de banco de dados do SQL Server e Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/bb510589).

## Segurança da Conexão

A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo servidor e pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não foram incluídos explicitamente na lista branca. Para permitir que seu aplicativo ou o endereço IP público do computador cliente tente se conectar a um novo banco de dados, primeiramente você deve criar uma regra de firewall no nível de servidor usando o Portal de Gerenciamento do Azure, a API REST ou o PowerShell. Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor. Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/ee621782).

Todas as conexões do Banco de Dados SQL do Azure exigem criptografia (SSL/TLS) todo o tempo que os dados estiverem "em trânsito", entrando e saindo do banco de dados. Na cadeia de conexão do seu aplicativo, você deve especificar os parâmetros para criptografar a conexão, e *não* para confiar no certificado de servidor (esse processo será automático se você copiar a cadeia de conexão fora do Portal de Gerenciamento do Azure); caso contrário, a conexão não verificará a identidade do servidor e estará sujeita a ataques "man-in-the-middle". Para o driver do ADO.NET, por exemplo, esses parâmetros da cadeia de conexão são **Encrypt = True** e **TrustServerCertificate = False**. Para saber mais, confira [Criptografia da conexão e validação de certificado do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## Autenticação

A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:

 - **Autenticação do SQL**, que usa um nome de usuário e senha
 - **Autenticação do Active Directory do Azure**, que usa identidades gerenciadas pelo Active Directory do Azure e tem suporte para domínios gerenciados e ingressados

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo". Se desejar usar a Autenticação do Active Directory do Azure, você deve criar outro administrador de servidor chamado “admin do AD do Azure”, que tenha permissão para administrar usuários e grupos do AD do Azure. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum. Veja [Conectando-se ao Banco de Dados SQL usando a Autenticação do Active Directory do Azure](sql-database-aad-authentication.md) para ver um passo a passo de como criar um administrador do AD do Azure para habilitar a Autenticação do Active Directory do Azure.

Como uma prática recomendada, seu aplicativo deve usar uma conta diferente para autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. A abordagem recomendada é criar um [usuário de banco de dados independente](https://msdn.microsoft.com/library/ff929188), o que permite que o seu aplicativo seja autenticado diretamente em um banco de dados individual. Você pode criar um usuário de banco de dados independente que usa a Autenticação do SQL executando o seguinte comando T-SQL quando estiver conectado ao banco de dados do usuário com o logon de administrador do servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se você criou um administrador do AD do Azure, é possível criar um usuário de banco de dados independente que usa a autenticação do Active Directory do Azure executando o seguinte comando T-SQL quando estiver conectado ao banco de dados de usuário como o administrador do AD do Azure:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Em ambos os casos, a cadeia de conexão do aplicativo deve especificar essas credenciais de usuário, em vez do logon de administrador do servidor, para se conectar ao banco de dados.

Para saber mais sobre como fazer a autenticação em um Banco de Dados SQL, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).


## Autorização
A Autorização refere-se ao que você pode fazer em um Banco de Dados SQL do Azure, e isso é controlado pelas associações e permissões da função da sua conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. O Banco de Dados SQL do Azure facilita o gerenciamento deles com funções no T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db\_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Existem maneiras de limitar ainda mais o que um usuário pode fazer com o Banco de Dados SQL do Azure:

* [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121) diferentes de db\_datareader e db\_datawriter podem ser usadas para criar contas de usuário de aplicativo mais potentes ou contas de gerenciamento menos potentes.
* [Permissões](https://msdn.microsoft.com/library/ms191291) granulares permitem controlar quais operações você pode fazer em colunas, tabelas, exibições, procedimentos e outros objetos individuais no banco de dados.
* [Representação](https://msdn.microsoft.com/library/vstudio/bb669087) e [assinatura de módulo](https://msdn.microsoft.com/library/bb669102) podem ser usadas para elevar permissões temporariamente com segurança.
* [Segurança no nível de linha](https://msdn.microsoft.com/library/dn765131) permite filtrar quais linhas um usuário pode ver.
* [Mascaramento de dados](sql-database-dynamic-data-masking-get-started.md) pode ser usado para limitar a exposição de dados confidenciais.
* [Procedimentos armazenados](https://msdn.microsoft.com/library/ms190782) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O gerenciamento de bancos de dados e servidores lógicos pelo Portal de Gerenciamento do Azure ou usando a API do Gerenciador de Recursos do Azure é controlado pela atribuições de função da sua conta de usuário. Para saber mais sobre esse tópico, confira [Controle de acesso baseado em função no portal de Visualização do Azure](../role-based-access-control-configure.md).


## Criptografia

O Banco de Dados SQL do Azure pode ajudar a proteger seus dados ao criptografá-los quando estiverem “em repouso” ou armazenados nos arquivos e backups de banco de dados usando [Criptografia Transparente de Dados](http://go.microsoft.com/fwlink/?LinkId=526242). Para criptografar o banco de dados, conecte-se como um proprietário de banco de dados e execute:

```
CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_256 
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;
   
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Para outras maneiras de criptografar seus segredos de dados, considere:

* [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
* Se você precisar de um Módulo de Segurança de Hardware ou do gerenciamento central de sua hierarquia de chaves de criptografia, considere o uso do [cofre da chave do Azure com o SQL Server em uma VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


## Auditoria

A Auditoria e o rastreamento dos eventos de banco de dados podem ajudar você a manter a conformidade normativa e a identificar atividades suspeitas. A Auditoria do Banco de Dados SQL permite registrar eventos no banco de dados em um log de auditoria na sua conta de armazenamento do Azure. A Auditoria do Banco de Dados SQL também se integra ao Microsoft Power BI para facilitar análises e relatórios detalhados. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md).

## Conformidade

Além dos recursos e funcionalidades acima, que podem ajudar seu aplicativo a atender a vários requisitos de conformidade de segurança, o Banco de Dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para saber mais, confira a [Central de Confiabilidade do Microsoft Azure](http://azure.microsoft.com/support/trust-center/), onde é possível encontrar a lista mais atual de [certificações de conformidade do Banco de Dados SQL](http://azure.microsoft.com/support/trust-center/services/).
 

<!---HONumber=Sept15_HO4-->