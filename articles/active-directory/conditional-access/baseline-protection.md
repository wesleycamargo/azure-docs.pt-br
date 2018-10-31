---
title: O que é uma proteção de linha de base no acesso condicional do Azure Active Directory? - versão prévia | Microsoft Docs
description: Saiba como a proteção de linha de base garante que você tenha pelo menos o nível de linha de base de segurança ativado em seu ambiente do Azure Active Directory.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e7e92c2f3564591133816baaaf758d72b083330b
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958543"
---
# <a name="what-is-baseline-protection-preview"></a>O que é proteção de linha de base (versão prévia)?  

No ano passado, os ataques de identidade aumentaram em 300%. Para proteger seu ambiente contra ataques cada vez maiores, o Azure AD (Azure AD) apresenta um novo recurso chamado proteção de linha de base. A proteção de linha de base é um conjunto de [políticas de acesso condicional](../active-directory-conditional-access-azure-portal.md) predefinidas. O objetivo dessas políticas é garantir que você tenha pelo menos o nível de linha de base de segurança ativado em todas as edições do Azure AD. 

Este artigo fornece uma visão geral da proteção de linha de base no Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Exigir MFA para administradores

Usuários com acesso a contas privilegiadas têm acesso irrestrito a seu ambiente. Devido à capacidade que essas contas têm, devem ser tratadas com cuidado especial. Um método comum para melhorar a proteção de contas privilegiadas é exigir uma forma mais forte de verificação de conta quando elas são usadas para entrar. No Azure Active Directory, você pode obter uma verificação de conta mais forte exigindo MFA (autenticação multifator).  

**Exigir MFA para administradores** é uma política de linha de base que requer o MFA para as seguintes funções de diretório: 

- Administrador global  

- Administrador do SharePoint  

- Administrador do Exchange  

- Administrador de acesso condicional  

- Administrador de segurança  


![Azure Active Directory](./media/baseline-protection/01.png)

Essa política de linha de base fornece a opção de excluir usuários e grupos. Você pode querer excluir uma *[ conta administrativa de acesso de emergência ](../users-groups-roles/directory-emergency-access.md)* para garantir que você não seja bloqueado pelo locatário.


## <a name="enable-a-baseline-policy"></a>Habilitar uma política de linha de base 

Enquanto as políticas de linha de base estão em visualização, por padrão, elas não são ativadas. É necessário habilitar manualmente uma política para ativá-la. Se você habilitar explicitamente as políticas de linha de base no estágio de versão prévia, elas permanecerão ativas quando esse recurso atingir a disponibilidade geral. A alteração de comportamento planejada é o motivo pelo qual, além de ativar e desativar, há uma terceira opção para definir o estado de uma política: **Habilitar automaticamente a política futuramente**. Ao selecionar essa opção, você pode deixar as políticas desabilitadas durante a versão prévia, mas permitir que a Microsoft as habilite automaticamente quando esse recurso atingir a disponibilidade geral. Se você não habilitar explicitamente as políticas de linha de base agora e não selecionar a opção **Habilitar automaticamente a política futuramente**, as políticas permanecerão desabilitadas quando esse recurso atingir a disponibilidade geral.


**Para habilitar uma política de linha de base:**  

1. Faça login no [ portal do Azure ](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.

2. No **portal do Azure**, na barra de navegação à esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. Na página do **Active Directory do Azure**, na seção **Segurança**, clique em **Acesso condicional**.

    ![Acesso condicional](./media/baseline-protection/05.png)

4. Na lista de políticas, clique em uma política que começa com **Política de linha de base:**. 

5. Para ativar a política, clique em **Usar política imediatamente**.

6. Clique em **Salvar**. 
 
  
 

## <a name="what-you-should-know"></a>O que você deve saber 

Embora o gerenciamento de políticas de acesso condicional personalizado exija uma licença do Azure AD Premium, políticas de linha de base estão disponíveis em todas as edições do Azure AD.     

As funções de diretório incluídas na política de linha de base são as funções mais privilegiadas do Azure AD. 

Se você tiver contas com privilégios que são usadas em scripts, deverá substituí-las por [identidades gerenciadas para recursos do Azure](../managed-identities-azure-resources/overview.md) ou [entidades de serviço com certificados](../develop/howto-authenticate-service-principal-powershell.md). Como solução temporária, você pode excluir contas de usuários específicas da política de linha de base. 

Políticas de linha de base se aplicam a fluxos de autenticação herdados, como POP, IMAP, cliente de desktop do Office antigo. 




## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

- [Cinco etapas para proteger a infraestrutura de identidade](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [O que é o acesso condicional no Azure Active Directory?](overview.md) 

