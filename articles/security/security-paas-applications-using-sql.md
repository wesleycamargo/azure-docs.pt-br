---
title: "Protegendo aplicativos Web e móveis de PaaS usando o Banco de Dados SQL e o SQL Data Warehouse | Microsoft Docs"
description: " Saiba mais sobre as práticas recomendadas de segurança do SQL Data Warehouse e do Banco de Dados SQL do Azure para proteger seus aplicativos Web e móveis de PaaS. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b3db1281bbd37fcfbec54bdeb998e6338bb08369
ms.openlocfilehash: 39f841c0f3c8bed1bd640dfd644a58a764422aff


---
# <a name="securing-paas-web-and-mobile-applications-using-sql-database-and-sql-data-warehouse"></a>Protegendo aplicativos Web e móveis de PaaS usando o Banco de Dados SQL e o SQL Data Warehouse

Neste artigo, discutiremos uma coleção de práticas recomendadas de segurança do [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) e do [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) para proteger seus aplicativos Web e móveis de PaaS. Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Banco de Dados SQL do Azure e SQL Data Warehouse
O [Banco de Dados SQL do Azure](../sql-database/sql-database-technical-overview.md) e o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) fornecem um serviço de banco de dados relacional para seus aplicativos baseados na Internet. Vamos examinar os serviços que ajudam a proteger seus aplicativos e dados ao usar o Banco de Dados SQL do Azure e o SQL Data Warehouse em uma implantação de PaaS:

- Autenticação do Azure Active Directory (em vez da autenticação do SQL Server)
- Firewall do SQL do Azure
- Transparent Data Encryption (TDE)

## <a name="best-practices"></a>Práticas recomendadas

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Usar um repositório de identidades centralizado para autenticação e autorização

Os bancos de dados SQL do Azure podem ser configurados para usar um dos dois tipos de autenticação:

- A **Autenticação do SQL** usa um nome de usuário e senha. Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados.

- A **Autenticação do Azure Active Directory** usa identidades gerenciadas pelo Azure Active Directory e que tem suporte para domínios gerenciados e integrados. Para usar a Autenticação do Azure Active Directory, você deve criar outro administrador de servidor chamado “admin do Azure AD”, que tenha permissão para administrar usuários e grupos do Azure AD. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum.

A [autenticação do Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) é um mecanismo de conexão com o Banco de Dados SQL do Azure e o SQL Data Warehouse usando identidades no Azure AD (Active Directory). O Azure AD fornece uma alternativa para a autenticação do SQL Server para que você possa parar a proliferação de identidades de usuário entre os servidores de banco de dados. A autenticação do Azure AD permite que você gerencie centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões.  

Os benefícios de usar a autenticação do Azure AD em vez da autenticação do SQL incluem:

- Permite o rodízio de senhas em um único lugar.
- Gerencia as permissões de banco de dados usando grupos externos do Azure AD.
- Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure AD.
- Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
- Dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
- Dá suporte ao ADFS (federação de domínio) ou à autenticação nativa de senha/usuário para um Azure AD local sem a sincronização de domínio.
- Dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o [MFA (Autenticação Multifator)](../multi-factor-authentication/multi-factor-authentication.md). A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para saber mais, confira [Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Para saber mais sobre a autenticação do Azure AD, consulte:

- [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Autenticação do Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Token-based authentication support for Azure SQL DB using Azure AD authentication](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/) (Suporte para autenticação baseada em token para o Banco de Dados SQL do Azure usando a autenticação do Azure AD)

> [!NOTE]
> Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), especificamente as considerações adicionais.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Restringir o acesso com base no endereço IP
Você pode criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Essas regras podem ser direcionadas nos níveis do servidor e do banco de dados. É recomendável o uso de regras de firewall no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil. As regras de firewall no nível do servidor são melhor usadas para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso, mas não quer gastar tempo configurando cada um individualmente.

As restrições de endereço IP de origem do Banco de Dados SQL permitem o acesso de qualquer endereço do Azure (incluindo outras assinaturas e locatários). Você pode restringir isso para permitir apenas que seus endereços IP acessem a instância. Mesmo com as restrições de endereço IP e firewall do SQL, a autenticação forte ainda é necessária. Consulte as recomendações feitas anteriormente neste artigo.

Para saber mais sobre as restrições de IP e o Firewall do SQL do Azure, consulte:

- [Controle de acesso do Banco de Dados SQL do Azure](../sql-database/sql-database-control-access.md)
- [Configurar regras de firewall do Banco de Dados SQL do Azure – visão geral](../sql-database/sql-database-firewall-configure.md)
- [Configurar uma regra de firewall no nível de servidor de Banco de Dados SQL do Azure usando o Portal do Azure](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso
A [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/azure/bb934049) criptografa arquivos de dados do SQL Server, Banco de Dados SQL do Azure e SQL Data Warehouse do Azure, conhecidos como criptografia de dados em repouso. Você pode adotar várias precauções para ajudar a proteger o banco de dados, como a criação de um sistema seguro, a criptografia de ativos confidenciais e a criação de um firewall em torno de servidores de bancos de dados. No entanto, em um cenário em que a mídia física (como unidades ou fitas de backup) é roubada, um terceiro mal-intencionado pode restaurar ou anexar o banco de dados e procurar os dados. Uma solução é criptografar os dados confidenciais no banco de dados e proteger as chaves usadas para criptografar os dados com um certificado. Isso impede que alguém sem as chaves use os dados, mas esse tipo de proteção deve ser planejado antecipadamente.

A TDE protege os dados em repouso, o que significa os arquivos de log e de dados. Ele fornece a capacidade de cumprir muitas leis, regulamentações e diretrizes estabelecidas em vários setores. Isso permite que os desenvolvedores de software criptografem dados usando algoritmos de criptografia padrão do setor sem alterar os aplicativos existentes.

A TDE deverá ser usada se as regulamentações especificarem explicitamente essa criptografia. Lembre-se, no entanto, que isso não vai parar um invasor usando o caminho de acesso normal. A TDE é usada para proteger contra o caso altamente improvável de você talvez precisar usar a criptografia adicional no nível do aplicativo, por meio da criptografia fornecida pelo SQL do Azure em relação às linhas e colunas ou por meio da criptografia do nível do aplicativo.

A criptografia do nível do aplicativo também deve ser usada para dados seletivos. As preocupações com a soberania de dados podem ser reduzidas com a criptografia de dados com uma chave que é mantida no país correto. Isso impede que até uma transferência de dados acidental cause um problema, uma vez que é impossível descriptografar os dados sem a chave, supondo que um algoritmo forte seja usado (como AES 256).

A criptografia fornecida pelo SQL do Azure em relação às linhas e colunas pode ser feita apenas para permitir apenas o acesso de usuários ([RBAC](../active-directory/role-based-access-built-in-roles.md)) autorizados e impede que usuários com menos privilégios vejam as colunas ou linhas.

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou uma coleção de práticas recomendadas de segurança do Banco de Dados SQL e SQL Data Warehouse para proteger seus aplicativos Web e móveis de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](security-paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](security-paas-applications-using-app-services.md) (Proteção dos aplicativos Web e móveis de PaaS usando os Serviços de Aplicativos do Azure)



<!--HONumber=Feb17_HO1-->


