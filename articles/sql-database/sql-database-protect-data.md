---
title: "Visão geral da proteção de dados do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a proteção de dados em um banco de dados SQL do Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>Proteção de dados em seu Banco de Dados SQL

O Banco de Dados SQL protege seus dados usando criptografia.  

## <a name="overview"></a>Visão geral

O Banco de Dados SQL protege dados, fornecendo criptografia de dados em movimento usando o [protocolo TLS](https://support.microsoft.com/en-us/kb/3135244), para dados em repouso usando [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242) e para dados em uso usando [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Para uma discussão sobre o uso desses recursos de proteção de dados no Banco de Dados SQL, veja [Segurança e proteção de dados](sql-database-protect-data.md).

Para outras maneiras de criptografar seus dados, considere:

* [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
* Se você precisar de um Módulo de Segurança de Hardware ou do gerenciamento central de sua hierarquia de chaves de criptografia, considere o uso do [cofre da chave do Azure com o SQL Server em uma VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


O Banco de Dados SQL protege dados ao fornecer os recursos de auditoria e de detecção de ameaças. 

### <a name="auditing"></a>Auditoria
A Auditoria do Banco de Dados SQL rastreia as atividades de banco de dados e ajuda a manter a conformidade normativa, registrando eventos de banco de dados em um log de auditoria em sua conta de Armazenamento do Azure. A auditoria permite que você compreenda as atividades do banco de dados em andamento e analise e investigue a atividade de histórico para identificar possíveis ameaças ou suspeitas de violações de segurança e de abuso. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Detecção de ameaças
A Detecção de Ameaças complementa a auditoria, fornecendo uma camada adicional de inteligência de segurança criada para o serviço Banco de Dados SQL. Ela funciona ininterruptamente para aprender, perfilar e detectar atividades anormais de banco de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso do banco de dados anormais. Você pode responder a alertas, seguindo as instruções informativas e acionáveis fornecidas. Para obter mais informações, confira [Introdução à Detecção de Ameaças do Banco de Dados SQL](sql-database-threat-detection-get-started.md).  

## <a name="connection-security"></a>Segurança da conexão
A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo servidor e pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não foram incluídos explicitamente na lista de permissões. Para permitir que seu aplicativo ou o endereço IP público do computador cliente tente se conectar a um novo banco de dados, primeiramente você deve criar uma regra de firewall no nível de servidor usando o Portal Clássico do Azure, a API REST ou o PowerShell. Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor. Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/ee621782).

Todas as conexões do Banco de Dados SQL do Azure exigem criptografia (SSL/TLS) todo o tempo que os dados estiverem "em trânsito", entrando e saindo do banco de dados. Na cadeia de conexão do seu aplicativo, você deve especificar os parâmetros para criptografar a conexão, e *não* para confiar no certificado do servidor (esse processo será automático se você copiar a cadeia de conexão fora do Portal Clássico do Azure); caso contrário, a conexão não pode verificar a identidade do servidor e está sujeita a ataques "man-in-the-middle". Para o driver do ADO.NET, por exemplo, esses parâmetros da cadeia de conexão são **Encrypt=True** e **TrustServerCertificate=False**. Para saber mais, confira [Criptografia da conexão e validação de certificado do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Autenticação
A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:

* **Autenticação do SQL**, que usa um nome de usuário e senha
* **Autenticação do Active Directory do Azure**, que usa identidades gerenciadas pelo Active Directory do Azure e que tem suporte para domínios gerenciados e integrados

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo". Se desejar usar a Autenticação do Active Directory do Azure, você deve criar outro administrador de servidor chamado “admin do AD do Azure”, que tenha permissão para administrar usuários e grupos do AD do Azure. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum. Veja [Conectando-se ao Banco de Dados SQL usando a Autenticação do Active Directory do Azure](sql-database-aad-authentication.md) para ver um passo a passo de como criar um administrador do Azure AD para habilitar a Autenticação do Active Directory do Azure.

Como uma prática recomendada, seu aplicativo deve usar uma conta diferente para autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. A abordagem recomendada é criar um [usuário de banco de dados independente](https://msdn.microsoft.com/library/ff929188), o que permite que o seu aplicativo seja autenticado diretamente no banco de dados individual. Você pode criar um usuário de banco de dados independente que usa a Autenticação do SQL executando o seguinte comando T-SQL quando estiver conectado ao banco de dados do usuário com o logon de administrador do servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se você criou um administrador do AD do Azure, é possível criar um usuário de banco de dados independente que usa a autenticação do Active Directory do Azure executando o seguinte comando T-SQL quando estiver conectado ao banco de dados de usuário como o administrador do AD do Azure:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Em ambos os casos, a cadeia de conexão do aplicativo deve especificar essas credenciais de usuário, em vez do logon de administrador do servidor, para se conectar ao banco de dados.

Para saber mais sobre como fazer a autenticação em um Banco de Dados SQL, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

## <a name="authorization"></a>Autorização
A Autorização refere-se ao que você pode fazer em um Banco de Dados SQL do Azure, e isso é controlado pelas associações e permissões da função da sua conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. O Banco de Dados SQL do Azure facilita o gerenciamento deles com funções no T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Existem maneiras de limitar ainda mais o que um usuário pode fazer com o Banco de Dados SQL do Azure:

* [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121) diferentes de db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais potentes ou contas de gerenciamento menos potentes.
* [Permissões](https://msdn.microsoft.com/library/ms191291) granulares permitem controlar quais operações você pode fazer em colunas, tabelas, exibições, procedimentos e outros objetos individuais no banco de dados.
* [Representação](https://msdn.microsoft.com/library/vstudio/bb669087) e [assinatura de módulo](https://msdn.microsoft.com/library/bb669102) podem ser usadas para elevar permissões temporariamente com segurança.
* [Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131) pode ser usado para limitar quais linhas de um usuário pode acessar.
* [Mascaramento de dados](sql-database-dynamic-data-masking-get-started.md) pode ser usado para limitar a exposição de dados confidenciais.
* [Procedimentos armazenados](https://msdn.microsoft.com/library/ms190782) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O gerenciamento de bancos de dados e servidores lógicos pelo Portal Clássico do Azure ou usando a API do Gerenciador de Recursos do Azure é controlado pelas atribuições de função da sua conta de usuário. Para saber mais sobre esse tópico, confira [Controle de acesso baseado em função no portal do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encryption"></a>Criptografia
O Banco de Dados SQL do Azure pode ajudar a proteger seus dados ao criptografá-los quando estiverem “em repouso” ou armazenados nos arquivos e backups de banco de dados usando [Criptografia Transparente de Dados](http://go.microsoft.com/fwlink/?LinkId=526242). Para criptografar o banco de dados, conecte-se como um proprietário de banco de dados e execute:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Para outras maneiras de criptografar seus segredos de dados, considere:

* [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
* Se você precisar de um Módulo de Segurança de Hardware ou do gerenciamento central de sua hierarquia de chaves de criptografia, considere o uso do [cofre da chave do Azure com o SQL Server em uma VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Sempre Criptografado](https://msdn.microsoft.com/library/mt163865.aspx) (na visualização) torna a criptografia transparente para os aplicativos e permite que os clientes criptografem dados confidenciais em aplicativos cliente sem compartilhar as chaves de criptografia com o Banco de Dados SQL.

## <a name="auditing"></a>Auditoria
A Auditoria e o rastreamento dos eventos de banco de dados podem ajudar você a manter a conformidade normativa e a identificar atividades suspeitas. A Auditoria do Banco de Dados SQL permite registrar eventos no banco de dados em um log de auditoria na sua conta de armazenamento do Azure. A Auditoria do Banco de Dados SQL também se integra ao Microsoft Power BI para facilitar análises e relatórios detalhados. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md).

A Detecção de Ameaças do Banco de Dados SQL fornece uma camada adicional de segurança sobre a auditoria. Ela permite que você responda às ameaças conforme elas ocorrem fornecendo alertas de segurança em atividades anômalas. Para obter mais informações, confira [Introdução à Detecção de Ameaças do Banco de Dados SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformidade
Além dos recursos e funcionalidades acima, que podem ajudar seu aplicativo a atender a vários requisitos de conformidade de segurança, o Banco de Dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para saber mais, confira a [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde é possível encontrar a lista mais atual de [certificações de conformidade do Banco de Dados SQL](https://azure.microsoft.com/support/trust-center/services/).


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de todos os recursos de segurança do Banco de Dados SQL, veja [Visão geral de segurança do SQL](sql-database-security-overview.md).
- Para uma discussão sobre o uso de recursos de controle de acesso no Banco de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para obter uma discussão sobre monitoramento proativo, veja [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md) e [Introdução à Detecção de Ameaças do Banco de Dados SQL](sql-database-threat-detection-get-started.md).



<!--HONumber=Dec16_HO4-->


