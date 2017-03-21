---
title: "Configurar a autenticação multifator – SQL do Azure | Microsoft Docs"
description: Use o Multi-Factor Authentication com SSMS para Banco de Dados SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: b134999d407195aaf44babb3e4862b96cc1dc1ed
ms.openlocfilehash: b36f0cf8cbf0dfb310d6dd534906ee5391ce4cd5
ms.lasthandoff: 03/02/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>Configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio

Esse tópico mostra como configurar Autenticação Multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio. 

Para obter uma visão geral da Autenticação Multifator de Banco de Dados SQL do Azure, consulte [Visão geral de Autenticação Multifator do Banco de Dados SQL do SQL Server Management Studio](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Etapas da configuração

1. **Configurar um Azure Active Directory**: para saber mais, confira [Integração de suas identidades locais com o Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar seu próprio nome de domínio ao Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) e [Gerenciar o Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar MFA** : para obter instruções passo a passo, consulte [Configurando a Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 
3. **Configurar Banco de Dados SQL ou o SQL Data Warehouse para autenticação do Azure AD** : para obter instruções passo a passo, consulte [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).
4. **Baixar o SSMS** - No computador cliente, baixe o SSMS mais recente (pelo menos de agosto de 2016) de [Baixar o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectando-se usando a autenticação universal com o SSMS

As etapas a seguir mostram como se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse usando o SSMS mais recente.

1. Para se conectar usando a Autenticação Universal, na caixa de diálogo **Conectar ao Servidor**, selecione **Active Directory Universal Authentication (Autenticação Universal do Active Directory)**.

   ![1mfa-universal-connect][1]
2. Como de costume para o Banco de Dados SQL e o SQL Data Warehouse, você deve clicar em **Opções** e especificar o banco de dados na caixa de diálogo **Opções**. E clique em **Conectar**.
3. Quando a caixa de diálogo **Conectar-se à sua conta** aparecer, forneça a conta e a senha de sua identidade do Azure Active Directory.

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > Para a Autenticação Universal com uma conta que não exige o MFA, você se conecta neste momento. Para usuários que necessitam de MFA, continue com as etapas a seguir.
   > 
   > 
4. Devem aparecer duas caixas de diálogo de configuração de MFA. Essa operação única depende da configuração de administrador de MFA e, portanto, pode ser opcional. Para um domínio habilitado para MFA, essa etapa às vezes é predefinida (por exemplo, o domínio requer que os usuários usem um cartão inteligente e um PIN).  

   ![3mfa-setup][3]
5. A segunda caixa de diálogo única possível permite que você selecione os detalhes do seu método de autenticação. As possíveis opções são configuradas pelo administrador.

   ![4mfa-verify-1][4]
6. O Azure Active Directory envia as informações de confirmação para você. Quando receber o código de verificação, insira-o na caixa **Digite o código de verificação** e clique em **Entrar**.

   ![5mfa-verify-2][5]

Quando a verificação for concluída, o SSMS se conectará normalmente considerando as credenciais válidas e o acesso ao firewall.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral da Autenticação Multifator de Banco de Dados SQL do Azure, consulte [Visão geral de Autenticação Multifator do Banco de Dados SQL do SQL Server Management Studio](sql-database-ssms-mfa-authentication.md).
* Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](sql-database-manage-logins.md)  
Verifique se os outros podem se conectar pelo firewall: [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


