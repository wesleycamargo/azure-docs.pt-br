---
title: Atribuir ou remover licenças - Azure Active Directory | Microsoft Docs
description: Instruções sobre como atribuir ou remover licenças do Azure Active Directory de seus usuários ou grupos.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 78aef5d6627636ed0a28d81d902246fa318197a8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099272"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Atribuir ou remover licenças usando o portal do Azure Active Directory
Muitos serviços do Azure AD (Azure Active Directory) exigem que você ative um produto do Azure AD e licencie cada um dos seus usuários ou grupos (e membros associados) para esse produto. Somente usuários com licenças ativas poderão acessar e usar os serviços licenciados do Azure AD.

## <a name="available-product-editions"></a>Edições do produto disponível
Há várias edições do produto do AD do Azure.

- AD do Azure Gratuito

- AD Basic do Azure

- O Azure AD Premium 1 (Azure AD P1)

- O Azure AD Premium 2 (Azure AD P2)

Para informações específicas sobre cada edição do produto e os detalhes de licenciamento associados, consulte [De qual licença eu preciso?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Exiba os detalhes de edição e a licença do produto
Você pode visualizar seus produtos disponíveis, incluindo as licenças individuais, verificando as datas de expiração pendentes e o número de atribuições disponíveis.

### <a name="to-find-your-product-and-license-details"></a>Para encontrar os detalhes do seu produto e licença
1. Faça login no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador global para o diretório.

2. Selecione **Active Directory do Azure** e, em seguida, selecione **Licenças**.

    O **licenças** página será exibida.

    ![Página Licenças, mostrando o número de produtos adquiridos e licenças atribuídas](media/license-users-groups/license-details-blade.png)
    
3. Selecione o link **Produtos comprados** para visualizar a página **Produtos** e para ver os **atribuídos**, **Disponíveis** e **expirados em breve** detalhes para cada edição de produto específica.

    ![Página de produtos, com edições de produto e informações de licença associadas](media/license-users-groups/license-products-blade-with-products.png)

4. Selecione um nome de edição do produto para ver seus usuários e grupos licenciados.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a usuários ou grupos
Certifique-se de que qualquer pessoa que precise usar um serviço licenciado do Azure AD tenha a licença apropriada. Cabe a você se deseja adicionar os direitos de licenciamento a usuários individuais ou a um grupo inteiro.

>[!Note]
>O licenciamento baseado em grupo é um recurso de visualização pública do Azure AD e está disponível com qualquer plano de licença pago do Azure AD. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Para obter informações detalhadas sobre como adicionar usuários, consulte [Como adicionar ou excluir usuários no Active Directory do Azure](add-users-azure-active-directory.md). Para obter informações detalhadas sobre como criar grupos e adicionar membros, consulte [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Para atribuir uma licença a um usuário específico
1. Na página **Produtos**, selecione o nome da edição que você deseja atribuir ao usuário. Por exemplo, _Plano Premium do Azure Active Directory 2_.

    ![Página de produtos, com edição destacada do produto](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Na página **Plano 2 do Active Directory do Azure 2**, selecione **Atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Na página **Atribuir**, selecione **Usuários e grupos** e pesquise e selecione o usuário que você está atribuindo a licença. Por exemplo, _Mary Parker_.

    ![Atribuir página de licença, com pesquisa realçada e opções de seleção](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Selecione **Opções de atribuição**, verifique se você tem as opções de licença apropriadas ativadas e, em seguida, selecione **OK**.

    ![Página de opção de licença mostrando todas as opções disponíveis na edição](media/license-users-groups/license-option-blade-assignments.png)

    A página **Atribuir licença** é atualizada para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    >[!NOTE]
    >Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um usuário, você deve especificar o **Local de uso**. Você pode definir esse valor **Azure Active Directory &gt; os usuários &gt; perfil &gt; configurações** área no Azure AD.

5. Selecione **Atribuir**.

    O usuário é adicionado à lista de usuários licenciados e tem acesso aos serviços incluídos do Azure AD.

### <a name="to-assign-a-license-to-an-entire-group"></a>Para atribuir uma licença a um grupo inteiro
1. Na página **Produtos**, selecione o nome da edição que você deseja atribuir ao usuário. Por exemplo, _Plano Premium do Azure Active Directory 2_.

    ![Lâmina de produtos, com edição destacada do produto](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Na página **Plano 2 do Active Directory do Azure 2**, selecione **Atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Na página **Atribuir**, selecione **Usuários e grupos** e pesquise e selecione o grupo que você está atribuindo a licença. Por exemplo, _política de MDM - West_.

    ![Atribuir página de licença, com pesquisa realçada e opções de seleção](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Selecione **Opções de atribuição**, verifique se você tem as opções de licença apropriadas ativadas e, em seguida, selecione **OK**.

    ![Página de opção de licença mostrando todas as opções disponíveis na edição](media/license-users-groups/license-option-blade-group-assignments.png)

    A página **Atribuir licença** é atualizada para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    >[!NOTE]
    >Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um grupo, você deve especificar o **Local de uso** para todos os membros. Você pode definir esse valor **Azure Active Directory &gt; os usuários &gt; perfil &gt; configurações** área no Azure AD. Qualquer usuário cuja localização de uso não seja especificada herda a localização do locatário.

5. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso aos serviços incluídos do Azure AD.


## <a name="remove-a-license"></a>Remover uma licença
Você pode remover uma licença de um usuário ou grupo da página **Licenças**.

### <a name="to-remove-a-license-from-a-specific-user"></a>Para remover uma licença de um usuário específico
1. Na página **Usuários Licenciados** da edição do produto, selecione o usuário que não deve mais ter a licença. Por exemplo, _Alain Charon_.

2. Selecione **remover licença**.

    ![Página de usuários licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença de um grupo
1. Na página **Grupos licenciados** da edição do produto, selecione o grupo que não deve mais ter a licença. Por exemplo, _política de MDM - West_.

2. Selecione **remover licença**.

    ![Página Grupos de Licenciados com a opção Remover Licença Destacada](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Herdada por um usuário de um grupo de licenças não podem ser removidas diretamente. Em vez disso, você precisa remover o usuário do grupo do qual ele está herdando a licença.

## <a name="next-steps"></a>Próximas etapas
Depois de atribuir suas licenças, você pode executar os seguintes processos:

- [Identifique e resolva problemas de atribuição de licenças](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Adicione usuários licenciados a um grupo para licenciamento](../users-groups-roles/licensing-groups-migrate-users.md)

- [Cenários, limitações e problemas conhecidos usando grupos para gerenciar o licenciamento no Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)
