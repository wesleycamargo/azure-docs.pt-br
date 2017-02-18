---
title: "Visão geral do servidor lógico do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Esta página fornece considerações e diretrizes para trabalhar com servidores lógicos do SQL Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 79a9e72d29b5522dc3960b79bae7876f21acb4c5
ms.openlocfilehash: 07181e5d35703cddf8a896badd45e7485c9e07a2


---
# <a name="azure-sql-database-logical-servers"></a>Servidores lógicos do Banco de Dados SQL do Azure

Este tópico fornece diretrizes e considerações para trabalhar com servidores lógicos do SQL Azure. Para obter informações sobre bancos de dados SQL do Azure, consulte [Bancos de dados SQL](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>O que é um servidor lógico do Banco de Dados SQL do Azure?
Um servidor lógico do Banco de Dados SQL do Azure atua como um ponto administrativo central para vários bancos de dados. No Banco de Dados SQL, um servidor é um constructo lógico diferente de uma instância do SQL Server com a qual você talvez esteja familiarizado no mundo local. Especificamente, o serviço do Banco de Dados SQL não faz garantias da localização dos bancos de dados em relação a seus servidores lógicos e não expõe nenhum acesso ou recursos no nível da instância.  

Um servidor lógico do Banco de Dados do Azure:

- É criado em uma assinatura do Azure, mas pode ser movido com seus recursos independentes para outra assinatura
- É o recurso pai de bancos de dados, pools elásticos e data warehouses
- Fornece um namespace para bancos de dados, pools elásticos e data warehouses
- É um contêiner lógico com semântica de tempo de vida forte – exclua um servidor e ele exclui os bancos de dados independentes, pools elásticos e data warehouses
- Participa do RBAC (controle de acesso baseado em função) do Azure: os bancos de dados e pools elásticos em um servidor herdam direitos de acesso do servidor
- É um elemento de ordem superior da identidade de bancos de dados e pools elásticos para finalidades de gerenciamento de recursos do Azure (consulte o esquema de URL para pools e bancos de dados)
- Coloca recursos em uma região
- Fornece um ponto de extremidade de conexão para acesso ao banco de dados (<serverName>.database.windows.net)
- Fornece acesso aos metadados sobre os recursos independentes por meio de DMVs, conectando-se ao banco de dados mestre 
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, auditoria, detecção de ameaças, etc. 
- É restrito por uma cota na assinatura pai (seis servidores por assinatura – [consulte os Limites da assinatura aqui](../azure-subscription-service-limits.md))
- Fornece o escopo da cota de banco de dados e da cota de DTU para os recursos que ele contém (como 45000 DTUs na V12)
- É o escopo de controle de versão para as funcionalidades habilitadas em recursos independentes (a última versão é V12)
- Os logons de entidade de segurança no nível do servidor podem gerenciar todos os bancos de dados em um servidor
- Pode conter logons semelhantes aos das instâncias do SQL Server locais que têm permissão para acessar um ou mais bancos de dados no servidor, além de poder receber direitos administrativos limitados. Para obter mais informações, consulte [Logons](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Como faço para me conectar e autenticar em um servidor lógico do Banco de Dados SQL do Azure?

- **Autenticação e autorização**: o Banco de Dados SQL do Azure dá suporte à autenticação do SQL e à Autenticação do Azure Active Directory (com algumas limitações – consulte [Conectar-se ao Banco de Dados SQL com a Autenticação do Azure Active Directory](sql-database-aad-authentication.md) para autenticação. Você pode se conectar e autenticar em bancos de dados SQL do Azure por meio do banco de dados mestre do servidor ou diretamente em um banco de dados de usuário. Para obter mais informações, consulte [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md). A Autenticação do Windows não é suportada. 
- **TDS**: o Banco de Dados SQL do Microsoft Azure dá suporte ao cliente de protocolo TDS versão 7.3 ou posterior.
- **TCP/IP**: são permitidas apenas conexões TCP/IP.
- **Firewall do Banco de Dados SQL**: para ajudar a proteger seus dados, o firewall do Banco de Dados SQL impede todo acesso ao servidor de banco de dados ou de seus bancos de dados até que você especifique quais computadores têm permissão. Consulte [Firewalls](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Há suporte para quais agrupamentos?

O agrupamento de banco de dados padrão usado pelo Banco de Dados SQL do Microsoft Azure (incluindo o banco de dados mestre) é **SQL_LATIN1_GENERAL_CP1_CI_AS**, em que **LATIN1_GENERAL** é inglês (Estados Unidos), **CP1** é a página de código 1252, **CI** diferencia maiúsculas de minúsculas e **AS** diferencia acentos. Não é recomendável alterar o agrupamento de bancos de dados V12 após a criação. Para obter mais informações sobre agrupamentos, consulte [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Quais são os requisitos de nomenclatura para objetos de banco de dados?

Os nomes de todos os novos objetos devem ser compatíveis com as regras do SQL Server para identificadores. Para saber mais, veja [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>Há suporte para quais recursos?

Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md). Consulte também [Diferenças do Transact-SQL do Banco de Dados SQL do Azure](sql-database-transact-sql-information.md) para obter mais informações sobre os motivos da falta de suporte para determinados tipos de recursos.

## <a name="how-do-i-manage-a-logical-server"></a>Como faço para gerenciar um servidor lógico?

Você pode gerenciar servidores lógicos do Banco de Dados SQL do Azure usando vários métodos:
- [portal do Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md)
- Para obter uma visão geral de bancos de dados SQL do Azure, consulte [Visão geral do banco de dados SQL](sql-database-overview.md)
- Para obter informações sobre o suporte ao Transact-SQL e as diferenças, consulte [Diferenças do Transact-SQL do Banco de Dados SQL do Azure](sql-database-transact-sql-information.md).
- Para obter informações sobre cotas e limitações de recursos específicos com base na **camada de serviço**. Para obter uma visão geral das camadas de serviço, consulte [Camadas de serviço do banco de dados SQL](sql-database-service-tiers.md).
- Para obter uma visão geral de segurança, veja [Visão geral de segurança do Banco de Dados SQL do Azure](sql-database-security-overview.md).
- Para obter informações sobre a disponibilidade de drivers e o suporte ao Banco de Dados SQL, veja [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md).




<!--HONumber=Feb17_HO1-->


