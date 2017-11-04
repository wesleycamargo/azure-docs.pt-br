---
title: "Migrar políticas clássicas no portal do Azure | Microsoft Docs"
description: "Migrar políticas clássicas no portal do Azure."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrar políticas clássicas no portal do Azure 


[Acesso condicional](active-directory-conditional-access-azure-portal.md) é uma funcionalidade do Azure Active Directory (Azure AD) que permite controlar como os usuários autorizados acessam seus aplicativos de nuvem. Enquanto a finalidade ainda é a mesma, a versão do novo portal do Azure também introduziu melhorias significativas em relação ao modo como funciona o acesso condicional. As políticas de acesso condicional que você configurou fora do portal do Azure podem coexistir com as novas políticas que você está criando no portal do Azure. Desde que você não esteja desabilitando ou removendo-as, elas ainda são aplicadas em seu ambiente. No entanto, é recomendável que você migre suas políticas clássicas para as novas políticas de acesso condicional do Azure AD pois:

- As novas políticas permitem que você lide com cenários que antes você não podia lidar com as políticas clássicas.

- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   

Este tópico ajuda com a migração de suas políticas clássicas existentes para as novas políticas de acesso condicional do Azure AD.


## <a name="classic-policies"></a>Políticas clássicas

As políticas de acesso condicional do Azure AD e do Intune que você não criou no portal do Azure também são conhecidas como **políticas clássicas**. Para migrar suas políticas clássicas, você não precisa ter acesso ao portal clássico do Azure. O portal do Azure fornece a você uma exibição [ das ****Políticas clássicas (versão prévia)](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) que permite que você analise suas políticas clássicas.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Abrir uma política clássica

**Para abrir uma política clássica:**

1. No [portal do Azure](https://portal.azure.com), na barra de navegação à esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/02.png)
 
2. Na página **Acesso condicional - Políticas**, na seção **Gerenciar**, clique em **Políticas clássicas (versão prévia)**.

3. Na lista de políticas clássicas, selecione a política importante para você.   

    ![Acesso condicional](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Políticas de acesso condicional do Azure AD

Este tópico fornece a você as etapas detalhadas que permitem migrar suas políticas clássicas sem a necessidade de estar familiarizado com as políticas de acesso condicional do Azure AD. No entanto, estar familiarizado com os conceitos básicos e a terminologia de acesso condicional do Azure AD ajuda a melhorar sua experiência de migração.

Consulte:

- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md) para saber mais sobre a terminologia e os conceitos básicos

- [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) para se familiarizar com a interface do usuário no portal do Azure


 





## <a name="multi-factor-authentication-policy"></a>Política de autenticação multifator 

Este exemplo mostra como migrar uma política clássica que exige autenticação multifator** para um aplicativo de nuvem. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**Para migrar uma política clássica:**

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
2. Crie uma nova política de acesso condicional do Azure AD para substituir a política clássica. 


### <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional


1. No [portal do Azure](https://portal.azure.com), na barra de navegação à esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/02.png)



3. Na página **Acesso condicional**, para abrir a página **Novo**, na barra de ferramentas na parte superior, clique em **Adicionar**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/03.png)

4. Na página **Novo**, na caixa de texto **Nome**, digite um nome para a política.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/29.png)

5. Na seção **Atribuições**, clique em **Usuários e grupos**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/05.png)

    a. Se você tiver todos os usuários selecionados em sua política clássica, clique em **Todos os usuários**. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/35.png)

    b. Se você tiver todos os grupos selecionados em sua política clássica, clique em **Selecionar usuários e grupos** e, em seguida, selecione os usuários e grupos desejados.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/36.png)

    c. Se você tiver os grupos excluídos, clique na guia **Excluir** e, em seguida, selecione os usuários e grupos necessários. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/37.png)

6. Na página **Novo**, para abrir a página **Aplicativos de nuvem**, na seção **Atribuição**, clique em **Aplicativos de nuvem**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Na página **Aplicativos de nuvem**, execute as seguintes etapas:

    ![Acesso condicional](./media/active-directory-conditional-access-migration/08.png)

    a. Clique em **Selecionar aplicativos**.

    b. Clique em **Selecionar**.

    c. Na página **Selecionar**, selecione seu aplicativo de nuvem e, em seguida, clique em **Selecionar**.

    d. Na página **Aplicativos de nuvem**, clique em **Concluído**.



9. Se você tiver selecionado **Exigir autenticação multifator**:

    ![Acesso condicional](./media/active-directory-conditional-access-migration/26.png)

    a. Na seção **Controles de acesso**, clique em **Conceder**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/27.png)

    b. Na página **Conceder**, clique em **Conceder acesso** e, em seguida, clique em **Exigir autenticação multifator**.

    c. Clique em **Selecionar**.


10. Clique em **Ativar** para habilitar a sua política.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/30.png)

11. Desabilite a política clássica. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 
