---
title: Alterar a senha da conta do Azure AD Connector | Microsoft Docs
description: Este tópico documenta como restaurar a conta do Azure AD Connector.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d68c190b51b9bbb5faf21e8ea75b07d1a82005e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571408"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Alterar a senha da conta do Azure AD Connector
A conta do Azure AD Connector deve ter serviço gratuito. Se você precisa redefinir suas credenciais, este tópico é indicado para você. Por exemplo, se um Administrador Global tem por engano redefinir a senha da conta usando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta do Azure AD Connector não pode contatar o Azure AD devido a problemas de autenticação, a senha pode ser redefinida.

1. Entre no servidor de sincronização do Azure AD Connector e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.  
   ![Cmdlet addadsyncaadserviceaccount do PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de Administrador Global do Azure AD.

Esse cmdlet redefinirá a senha da conta de serviço e a atualizará no Azure AD e no mecanismo de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Problemas conhecidos que essas etapas podem resolver
Esta seção é uma lista de erros relatados por clientes que foram corrigidos por uma redefinição na conta do Azure AD Connector de credenciais.

- - -
Evento 6900  
O servidor encontrou um erro inesperado ao processar uma notificação de alteração de senha:  
AADSTS70002: Erro ao validar credenciais. AADSTS50054: Uma senha antiga está sendo usada para autenticação.

- - -
Evento 659  
Erro ao recuperar a configuração de sincronização de política de senha. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Erro ao validar credenciais. AADSTS50054: Uma senha antiga está sendo usada para autenticação.

## <a name="next-steps"></a>Próximas etapas
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)

