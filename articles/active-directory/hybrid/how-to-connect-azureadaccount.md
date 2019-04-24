---
title: 'Sincronização do Azure AD Connect: como gerenciar a conta de serviço do Azure Active Directory | Microsoft Docs'
description: Este tópico documenta como restaurar a conta de serviço do Azure AD.
services: active-directory
keywords: 'AADSTS70002, AADSTS50054: Como redefinir a senha da conta de serviço do Conector de sincronização do Azure AD Connect'
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/12/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: f88318c87e29567b40b5eacf10f3b6f259adee8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60350994"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronização do Azure AD Connect: como gerenciar a conta de serviço do Azure Active Directory
A conta de serviço usada pelo Azure AD Connector deve ter serviço gratuito. Se você precisa redefinir suas credenciais, este tópico é indicado para você. Por exemplo, se um Administrador Global tiver redefinido a senha por engano na conta de serviço usando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta de serviço definida no Azure AD Connector não puder contatar o Azure AD devido a problemas de autenticação, a senha poderá ser redefinida.

1. Entre no servidor de sincronização do Azure AD Connector e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.  
   ![Cmdlet addadsyncaadserviceaccount do PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de Administrador Global do Azure AD.

Esse cmdlet redefinirá a senha da conta de serviço e a atualizará no Azure AD e no mecanismo de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Problemas conhecidos que essas etapas podem resolver
Esta seção é uma lista de erros relatados por clientes que foram corrigidos por uma redefinição de credenciais na conta de serviço do Azure AD.

- - -
Evento 6900  
O servidor encontrou um erro inesperado ao processar uma notificação de alteração de senha:  
AADSTS70002: Erro ao validar credenciais. AADSTS50054: Uma senha antiga está sendo usada para autenticação.

- - -
Evento 659  
Erro ao recuperar a configuração de sincronização de política de senha. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Erro ao validar credenciais. AADSTS50054: Uma senha antiga está sendo usada para autenticação.

## <a name="next-steps"></a>Próximos passos
**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
- [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)


