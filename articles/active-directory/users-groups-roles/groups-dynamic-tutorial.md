---
title: Tutorial para adicionar e remover usuários automaticamente de associação de grupo dinâmica no Azure Active Directory
description: Neste tutorial, você usa grupos com regras de associação do usuário para adicionar ou remover usuários automaticamente
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 2119bb60cbdc36f62623ce0db52885e17f3d3006
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "40209130"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Tutorial: adicionar ou remover membros do grupo automaticamente

No Azure Active Directory (Azure AD), você pode adicionar ou remover usuários automaticamente para grupos de segurança ou grupos do Office 365, para que você não precise fazê-lo sempre manualmente. Sempre que alterar as propriedades de um usuário ou dispositivo, o Azure AD avalia todas as regras de grupo dinâmico em seu locatário para ver se a alteração deve adicionar ou remover membros.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um grupo preenchido automaticamente de usuários convidados de uma empresa parceira específica
> * Atribuir licenças ao grupo para os recursos específicos do parceiro para acesso pelos usuários convidados
> * Bônus: proteger o grupo **Todos os usuários** removendo os usuários convidados, de modo que, por exemplo, você pode dar aos usuários membros acesso somente aos sites internos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este recurso requer uma licença Azure AD Premium para você como o administrador global do locatário. Se você não tiver uma, no Azure AD, selecione **Licenças** > **Produtos** > **Experimentar/Comprar**.

Não é necessário atribuir licenças aos usuários para que eles sejam membros de grupos dinâmicos. Você precisa apenas do número mínimo de licenças do Azure AD Premium P1 disponíveis no locatário para cobrir todos esses usuários. 

## <a name="create-a-group-of-guest-users"></a>Criar um grupo de usuários convidados

Primeiro, você criará um grupo para os usuários convidados que são de uma única empresa parceira. Eles precisam de uma licença especial, portanto, geralmente é mais eficiente criar um grupo para essa finalidade.

1. Entre no portal do Azure (https://portal.azure.com) com uma conta que seja de administrador global para o seu locatário.
2. Selecione **Azure Active Directory** > **Grupos** > **Novo grupo**.
  ![selecione o novo comando de grupo](./media/groups-dynamic-tutorial/new-group.png)
3. Na folha **Grupo**:
  
  * Selecione **Segurança** como o tipo de grupo
  * Digite `Guest users Contoso` como o nome e a descrição para o grupo
  * Altere o **Tipo de associação** para **Usuário dinâmico**
  * Selecione **Adicionar consulta dinâmica**
  
4. Selecione **Regra avançada** e na caixa **Regra avançada**, digite: `(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`
5. Selecione **Adicionar consulta** para fechar a folha.
6. Na folha **Grupo**, selecione **Criar** para criar o grupo.

## <a name="assign-licenses"></a>Atribuir licenças

Agora que você tem o seu novo grupo, você pode aplicar as licenças que esses usuários parceiros precisam.

1. No Azure AD, selecione **Licenças**, selecione uma ou mais licenças e, em seguida, selecione **Atribuir**.
2. Selecione **Usuários e grupos** e selecione o grupo **Usuários convidados Contoso** e salve suas alterações.
3. As **opções de atribuição** permitem que você ative ou desative o serviço de planos incluídos nas licenças que você selecionou. Quando você fizer uma alteração, certifique-se de clicar em **OK** para salvar suas alterações.
4. Para concluir a atribuição, no painel **Atribuir licença**, clique em **Atribuir** na parte inferior do painel.

## <a name="remove-guests-from-all-users-group"></a>Remover convidados do grupo Todos os usuários

Talvez seu plano administrativo final seja atribuir todos os usuários convidados a seus próprios grupos por empresa. Agora você também pode alterar o grupo **Todos os usuários** para que ele seja reservado somente para os usuários membros no seu locatário. Em seguida, você pode usá-lo para atribuir aplicativos e licenças que são específicas para sua organização inicial.

   ![Alterar o grupo Todos os usuários para somente membros](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Limpar recursos

**Para remover o grupo de usuários convidados**

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja de Administrador Global para o seu locatário.
2. Selecione **Azure Active Directory** > **Grupos**. Selecione o grupo **Usuários convidados Contoso**, selecione as reticências (...) e, em seguida, selecione **Excluir**. Quando você excluir o grupo, quaisquer licenças atribuídas são removidas.

**Para restaurar o grupo Todos os Usuários**
1. Selecione **Azure Active Directory** > **Grupos**. Selecione o nome do grupo **Todos os usuários** para abrir o grupo.
1. Selecione **Regras de associação dinâmica**, apague todo o texto na regra e selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:
> [!div class="checklist"]
> * Criar um grupo de usuários convidados
> * Atribuir licenças ao seu novo grupo
> * Alterar o grupo Todos os usuários para somente membros

Avance para o próximo artigo para saber mais sobre os fundamentos de licenciamento baseado em grupos
> [!div class="nextstepaction"]
> [Noções básicas de licenciamento de grupos](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



