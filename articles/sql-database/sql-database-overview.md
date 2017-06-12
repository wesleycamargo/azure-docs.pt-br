---
title: "O que é um banco de dados do Azure SQL? | Microsoft Docs"
description: "Este artigo fornece uma visão geral de bancos de dados SQL do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: ebfc2a6a2e16140953abbb9da3b81702fcc26e60
ms.contentlocale: pt-br
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-overview"></a>Visão geral do banco de dados SQL do Azure
Este tópico fornece uma visão geral de bancos de dados SQL do Azure. Para obter informações sobre servidores lógicos do SQL Azure, consulte [Servidores lógicos](sql-database-server-overview.md).

## <a name="what-is-azure-sql-database"></a>O que é o banco de dados SQL do Azure?
Cada banco de dados no Banco de Dados SQL do Azure é associado a um servidor lógico. O banco de dados pode ser:

- Um banco de dados individual com seu [próprio conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTUs)
- Parte de um [pool elástico](sql-database-elastic-pool.md) que [compartilha um conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTUs)
- Parte de um [conjunto expandido de bancos de dados fragmentados](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que pode ser individual ou bancos de dados em pool
- Parte de um conjunto de bancos de dados que participa de um [padrão de design de SaaS multilocatário](sql-database-design-patterns-multi-tenancy-saas-applications.md), e cujos bancos de dados podem ser individuais ou bancos de dados em pool (ou ambos) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Como faço para me conectar e autenticar em um banco de dados SQL do Azure?

- **Autenticação e autorização**: o Banco de Dados SQL do Azure dá suporte à autenticação do SQL e à Autenticação do Azure Active Directory (com algumas limitações – consulte [Conectar-se ao Banco de Dados SQL com a Autenticação do Azure Active Directory](sql-database-aad-authentication.md) para autenticação. Você pode se conectar e autenticar em bancos de dados SQL do Azure por meio do banco de dados mestre do servidor ou diretamente em um banco de dados de usuário. Para obter mais informações, consulte [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md). A Autenticação do Windows não é suportada. 
- **TDS**: o Banco de Dados SQL do Microsoft Azure dá suporte ao cliente de protocolo TDS versão 7.3 ou posterior.
- **TCP/IP**: são permitidas apenas conexões TCP/IP.
- **Firewall do Banco de Dados SQL**: para ajudar a proteger seus dados, o firewall do Banco de Dados SQL impede todo acesso ao servidor de banco de dados ou de seus bancos de dados até que você especifique quais computadores têm permissão. Consulte [Firewalls](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Há suporte para quais agrupamentos?
O agrupamento de banco de dados padrão usado pelo Banco de Dados SQL do Microsoft Azure é **SQL_LATIN1_GENERAL_CP1_CI_AS**, em que **LATIN1_GENERAL** é inglês (Estados Unidos), **CP1** é a página de código 1252, **CI** diferencia maiúsculas de minúsculas e **AS** diferencia acentos. Para obter mais informações sobre como definir o agrupamento, veja [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Quais são os requisitos de nomenclatura para objetos de banco de dados?

Os nomes de todos os novos objetos devem ser compatíveis com as regras do SQL Server para identificadores. Para saber mais, veja [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Há suporte para quais recursos nos bancos de dados SQL do Azure?

Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md). Consulte também [Diferenças do Transact-SQL do Banco de Dados SQL do Azure](sql-database-transact-sql-information.md) para obter mais informações sobre os motivos da falta de suporte para determinados tipos de recursos.

## <a name="how-do-i-manage-an-azure-sql-database"></a>Como faço para gerenciar um banco de dados SQL do Azure?

Você pode gerenciar servidores lógicos do Banco de Dados SQL do Azure usando vários métodos:
- [portal do Azure](sql-database-manage-overview.md)
- [PowerShell](sql-database-manage-overview.md)
- [Transact-SQL](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md)
- Para obter uma visão geral dos servidores lógicos do SQL Azure, consulte [Visão geral do servidor lógico do Banco de Dados SQL](sql-database-server-overview.md)
- Para obter informações sobre o suporte ao Transact-SQL e as diferenças, consulte [Diferenças do Transact-SQL do Banco de Dados SQL do Azure](sql-database-transact-sql-information.md).
- Para obter informações sobre cotas e limitações de recursos específicos com base na **camada de serviço**. Para obter uma visão geral das camadas de serviço, consulte [Camadas de serviço do banco de dados SQL](sql-database-service-tiers.md).
- Para obter uma visão geral de segurança, veja [Visão geral de segurança do Banco de Dados SQL do Azure](sql-database-security-overview.md).
- Para obter informações sobre a disponibilidade de drivers e o suporte ao Banco de Dados SQL, veja [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md).


