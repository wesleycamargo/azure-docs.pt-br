---
title: Autenticar no SQL Data Warehouse do Microsoft Azure | Microsoft Docs
description: Saiba como autenticar no SQL Data Warehouse do Azure usando a autenticação do Microsoft Azure Active Directory ou do Microsoft SQL Server.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: ab439566da38a537534fc10e70058ab57005ce22
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183791"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autenticar no SQL Data Warehouse do Azure
Saiba como autenticar no SQL Data Warehouse do Azure usando a autenticação do Microsoft Azure Active Directory ou do Microsoft SQL Server.

Para se conectar ao SQL Data Warehouse, você precisa inserir credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.  

Para obter mais informações sobre segurança e como habilitar conexões ao data warehouse, consulte [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Autenticação do SQL
Para se conectar ao SQL Data Warehouse, deve fornecer as seguintes informações:

* Nome totalmente qualificado do servidor
* Especificar a autenticação SQL
* Nome de Usuário
* Senha
* Banco de dados padrão (opcional)

Por padrão, a conexão se conecta ao banco de dados *mestre* e não ao banco de dados do usuário. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

* Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT, SSMS ou em sua cadeia de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog em uma conexão ODBC.
* Destaque o banco de dados do usuário antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **USE MyDatabase;** não tem suporte para alterar o banco de dados para uma conexão. Para obter as diretrizes de conexão ao SQL Data Warehouse com o SSDT, consulte o artigo [Consultar com o Visual Studio][Query with Visual Studio].
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do AAD (Azure Active Directory)
A autenticação do [Azure Active Directory][What is Azure Active Directory] é um mecanismo de conexão ao SQL Data Warehouse do Microsoft Azure usando identidades no Azure AD (Azure Active Directory). Com a autenticação do Azure Active Directory, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de ID fornece um único local para gerenciar usuários do SQL Data Warehouse e simplifica o gerenciamento de permissões. 

### <a name="benefits"></a>Benefícios
Os benefícios do Azure Active Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
* Permite o rodízio de senhas em um único lugar
* Gerencia as permissões de banco de dados usando grupos externos (AAD).
* Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação compatíveis com o Azure Active Directory.
* Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
* Dá suporte à autenticação baseada em token para aplicativos que se conectam ao SQL Data Warehouse.
* Dá suporte ao Multi-Factor Authentication por meio de Autenticação Universal do Active Directory para SQL Server Management Studio. Para obter uma descrição de Autenticação Multifator, consulte [Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> O Azure Active Directory ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory é uma boa opção para seu ambiente, consulte [Limitações e recursos do Azure AD][Azure AD features and limitations], especificamente, as Considerações adicionais.
> 
> 

### <a name="configuration-steps"></a>Etapas da configuração
Siga estas etapas para configurar a autenticação do Azure Active Directory.

1. Criar e popular um Azure Active Directory
2. Opcional: Associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Criar um administrador do Azure Active Directory para o SQL Data Warehouse do Azure
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conectar-se ao data warehouse usando identidades do Azure AD

Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Localização dos detalhes
* As etapas para configurar e usar a autenticação do Azure Active Directory são quase idênticas para o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Siga as etapas detalhadas no tópico [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte o [Guia de introdução às Permissões do Mecanismo do Banco de Dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Próximas etapas
Para começar a consultar o data warehouse com o Visual Studio e outros aplicativos, consulte [Consultar com o Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
