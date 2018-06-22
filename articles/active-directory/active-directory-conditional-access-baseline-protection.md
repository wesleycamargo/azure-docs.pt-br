---
title: O que é uma proteção de linha de base no acesso condicional do Active Directory do Azure | Microsoft Docs
description: Saiba como a proteção de linha de base garante que você tenha pelo menos o nível de linha de base de segurança ativado em seu ambiente.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248904"
---
# <a name="what-is-baseline-protection"></a>O que é proteção de linha de base?  

No ano passado, os ataques de identidade aumentaram em 300%. Para proteger seu ambiente contra ataques cada vez maiores, o Azure AD (Azure AD) apresenta um novo recurso chamado proteção de linha de base. A proteção de linha de base é um conjunto de políticas de acesso condicional predefinidas. O objetivo dessas políticas é garantir que você tenha pelo menos o nível de linha de base de segurança ativado em seu ambiente. 

Durante a visualização, você precisa ativar as políticas de linha de base se quiser ativá-las. Após a disponibilidade geral, essas políticas são ativadas por padrão. 

A primeira política de proteção de linha de base requer o MFA para contas com privilégios. Os invasores que obtêm o controle de contas com privilégios podem causar danos tremendos, portanto, é essencial proteger essas contas primeiro. As seguintes funções privilegiadas estão no escopo desta política: 

- Administrador global  

- Administrador do SharePoint  

- Administrador do Exchange  

- Administrador de acesso condicional  

- Administrador de segurança  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Como começar 

Para ativar a política de linha de base:  

1. Faça login no [ portal do Azure ](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.

2. No **portal do Azure**, na barra de navegação à esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-baseline-protection/03.png)

4. Na lista de políticas, clique em **Política de linha de base: Exigir MFA para administradores (visualização)**. 

5. Para ativar a política, clique em **Usar política imediatamente**.

6. Clique em **Salvar**. 
 

A política de linha de base fornece a opção de excluir usuários e grupos. Você pode querer excluir uma *[ conta administrativa de acesso de emergência ](active-directory-admin-manage-emergency-access-accounts.md)* para garantir que você não seja bloqueado pelo locatário.
  
 

## <a name="what-you-should-know"></a>O que você deve saber 

As funções de diretório incluídas na política de linha de base são as funções mais privilegiadas do Azure AD. Com base no feedback, outros podem ser incluídos no futuro. 

Se você tiver contas com privilégios de administrador em seus scripts, deverá usar [ Identidade de serviço gerenciada (MSI) ](managed-service-identity/overview.md) ou [ entidades de serviço (com certificados) ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). Como solução temporária, você pode excluir contas de usuários específicas da sua política de linha de base. 

A política se aplica a fluxos de autenticação herdados, como POP, IMAP, cliente de desktop do Office antigo. 

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 
