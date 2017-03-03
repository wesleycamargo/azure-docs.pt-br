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
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse
O Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure agora dão suporte a conexões do SSMS (SQL Server Management Studio) usando a *Autenticação Universal do Active Directory*. A Autenticação Universal do Active Directory é um fluxo de trabalho interativo que dá suporte ao *Azure MFA (Multi-Factor Authentication)* . O Azure MFA ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação eficiente com uma variedade de opções de verificação fáceis, como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis, os quais permitem que os usuários escolham seu método de preferência. 

* Para encontrar uma descrição do Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
* Para etapas de configuração, consulte [Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

## <a name="multi-factor-options"></a>Opções multifator

O SSMS agora dá suporte:

* MFA interativo com o Azure AD com o potencial para validação de caixa de diálogo pop-up.
* Métodos de Autenticação Integrada do Active Directory e Senha do Active Directory não interativos que podem ser usados em muitos aplicativos diferentes (ADO.NET, JDBC, ODBC etc.). Esses dois métodos nunca resultam em caixas de diálogo pop-up.

Quando a conta de usuário é configurada para MFA, o fluxo de trabalho de autenticação interativa requer interação adicional do usuário por meio de caixas de diálogo pop-up, o uso de cartão inteligente etc. Quando a conta de usuário é configurada para MFA, o usuário deve selecionar a Autenticação Universal do Azure para se conectar. Se a conta de usuário não exigir MFA, o usuário ainda poderá usar as outras duas opções de Autenticação do Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações da Autenticação Universal para o Banco de Dados SQL e SQL Data Warehouse
* O SSMS é a única ferramenta atualmente habilitada para MFA por meio da Autenticação Universal do Active Directory.
* Apenas uma única conta do Azure Active Directory pode fazer logon para uma instância do SSMS usando a Autenticação Universal. Para fazer logon como outra conta do Azure AD, você deve usar outra instância do SSMS. (Essa restrição é limitada à Autenticação Universal do Active Directory, você pode fazer logon em diferentes servidores usando a Autenticação de Senha do Active Directory, a Autenticação Integrada do Active Directory ou a Autenticação do SQL Server).
* O SSMS dá suporte à Autenticação Universal do Active Directory para a visualização do Pesquisador de Objetos, do Editor de Consultas e do Repositório de Consultas.
* O DacFx nem o Designer de Esquema dão suporte à Autenticação Universal.
* Não há suporte para contas MSA para a Autenticação Universal do Active Directory.
* Não há suporte para a Autenticação Universal do Active Directory no SSMS para usuários que são importados para o Active Directory atual de outros Active Directories do Azure. Esses usuários não têm suporte, pois isso exigiria uma ID de locatário para validar as contas e não há nenhum mecanismo para fornecer isso.
* Não há nenhum requisito de software adicional para a Autenticação Universal do Active Directory, exceto que você deve usar uma versão do SSMS com suporte.



## <a name="next-steps"></a>Próximas etapas

* Para etapas de configuração, consulte [Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](sql-database-manage-logins.md)  
Verifique se os outros podem se conectar pelo firewall: [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)



