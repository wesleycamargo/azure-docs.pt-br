---
title: "Autenticação Multifator do Azure - SQL do Azure | Microsoft Docs"
description: Use o Multi-Factor Authentication com SSMS para Banco de Dados SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 2a2ee58ba86fef089401521d727a301f8b59ce75
ms.lasthandoff: 04/07/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Autenticação Universal com o Banco de Dados SQL e SQL Data Warehouse (suporte SSMS para MFA)
O Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure dão suporte a conexões do SSMS (SQL Server Management Studio) usando a *Autenticação Universal do Active Directory*. 

- A Autenticação Universal do Active Directory dá suporte a dois métodos de autenticação não interativa (Autenticação de senha do Active Directory e Autenticação integrada do Active Directory). Métodos de Autenticação Integrada do Active Directory e Senha do Active Directory não interativos podem ser usados em muitos aplicativos diferentes (ADO.NET, JDBC, ODBC, etc.). Esses dois métodos nunca resultam em caixas de diálogo pop-up.

- A Autenticação Universal do Active Directory é um método interativo que também dá suporte ao *MFA (Autenticação Multifator do Azure)*. O Azure MFA ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação eficiente com uma variedade de opções de verificação fáceis, como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis, os quais permitem que os usuários escolham seu método de preferência. O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação.

Para encontrar uma descrição do Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Para etapas de configuração, consulte [Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parâmetro de ID do locatário ou nome de domínio do Azure AD   

Começando com o [SSMS versão 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), os usuários que são importados para o Active Directory atual de outros Azure Active Directories podem fornecer a ID de locatário ou nome de domínio do Azure AD quando eles se conectam. Isso permite que a **Autenticação Universal do Active Directory** identifique a autoridade de autenticação correta. Essa opção também é necessária para dar suporte a contas da Microsoft (MSA), como outlook.com, hotmail.com ou live.com. Todos esses usuários que desejam ser autenticados usando a autenticação Universal devem inserir sua ID de locatário ou nome de domínio do Azure AD. Esse parâmetro representa a ID de locatário/nome de domínio do Azure AD atual a que o Servidor do Azure está vinculado. Por exemplo, se o servidor do Azure está associado com o domínio do Azure AD `contosotest.onmicrosoft.com` em que o usuário `joe@contosodev.onmicrosoft.com` está hospedado como um usuário importado do domínio do Azure AD `contosodev.onmicrosoft.com`, o nome de domínio necessário para autenticar esse usuário é `contosotest.onmicrosoft.com`. Quando o usuário é um usuário nativo do Azure AD vinculado ao Servidor do Azure e não é uma conta MSA, nenhuma ID de locatário nem nome de domínio é necessário. Para inserir o parâmetro (começando com o SSMS versão 17) na caixa de diálogo **Conectar-se ao Banco de Dados**, complete a caixa de diálogo selecionando **Autenticação Universal do Active Directory**, clique em **Opções**, clique na guia **Propriedades de Conexão**, marque a caixa **ID de locatário ou nome de domínio do AD** e forneça a autoridade de autenticação, por exemplo o nome de domínio (**contosotest.onmicrosoft.com**) ou o guid da ID de locatário.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações da Autenticação Universal para o Banco de Dados SQL e SQL Data Warehouse
* O SSMS é a única ferramenta atualmente habilitada para MFA por meio da Autenticação Universal do Active Directory.
* Apenas uma única conta do Azure Active Directory pode fazer logon para uma instância do SSMS usando a Autenticação Universal. Para fazer logon como outra conta do Azure AD, você deve usar outra instância do SSMS. (Essa restrição é limitada à Autenticação Universal do Active Directory, você pode fazer logon em diferentes servidores usando a Autenticação de Senha do Active Directory, a Autenticação Integrada do Active Directory ou a Autenticação do SQL Server).
* O SSMS dá suporte à Autenticação Universal do Active Directory para a visualização do Pesquisador de Objetos, do Editor de Consultas e do Repositório de Consultas.
* O DacFx nem o Designer de Esquema dão suporte à Autenticação Universal.
* Não há nenhum requisito de software adicional para a Autenticação Universal do Active Directory, exceto que você deve usar uma versão do SSMS com suporte.


## <a name="next-steps"></a>Próximas etapas

* Para etapas de configuração, consulte [Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](sql-database-manage-logins.md)  
Verifique se os outros podem se conectar pelo firewall: [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)



