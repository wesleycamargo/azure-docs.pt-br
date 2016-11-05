---
title: Adicionar usuários de outros diretórios ou de empresas parceiras à visualização do Azure Active Directory | Microsoft Docs
description: Explica como adicionar usuários ou alterar as informações de usuário no Azure Active Directory, incluindo usuários externos e convidados.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Adicionar usuários de outros diretórios ou de empresas parceiras na visualização do Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-users-create-external-azure-portal.md)
> * [Portal clássico do Azure](active-directory-create-users-external.md)
> 
> 

Este artigo explica como adicionar usuários de outros diretórios no Azure AD (Azure Active Directory) ou de empresas parceiras. [O que há na visualização?](active-directory-preview-explainer.md) Para saber mais sobre como adicionar novos usuários à sua organização e como adicionar usuários com contas da Microsoft, confira [Add new users to Azure Active Directory](active-directory-users-create-azure-portal.md) (Adicionar novos usuários ao Azure Active Directory). Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## Adicionar um usuário
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.
   
   ![Abrir o gerenciamento de usuários](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)
3. Na folha **Usuários e grupos**, escolha **Usuários** e **Adicionar**.
   
   ![Selecionando o comando Adicionar](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)
4. Na folha **Usuário**, forneça um nome de exibição em **Nome** e o nome de entrada do usuário em **Nome de usuário**.
5. Copie ou anote a senha de usuário gerada para que você possa fornecê-la ao usuário depois que esse processo estiver concluído.
6. Se preferir, escolha **Perfil** para adicionar o nome e sobrenome dos usuários, um cargo e um nome de departamento.
   
        ![Opening the user profile](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)
   
   * Escolha **Grupos** para adicionar o usuário a um ou mais grupos.
     
       ![Adicionando um usuário aos grupos](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)
   * Escolha **Função organizacional** para atribuir o usuário a uma função na lista **Funções**. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md).
     
       ![Atribuindo um usuário a uma função](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)
7. Selecione **Criar**.
8. Distribua com segurança a senha gerada para o novo usuário para que ele possa entrar.

> [!IMPORTANT]
> Se sua organização usa mais de um domínio, você deve saber sobre os seguintes problemas ao adicionar uma conta de usuário:
> 
> * PARA adicionar contas de usuário com o mesmo nome UPN entre domínios, **primeiro** adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido de** geoffgrisso@contoso.com.
> * **Não** adicione geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Essa ordem é extremamente importante e pode ser inconveniente para desfazer.
> 
> 

Se você alterar as informações de um usuário cuja identidade esteja sincronizada com o serviço do Active Directory local, não será possível alterar as informações do usuário no portal clássico do Azure. Para alterar as informações do usuário, use suas ferramentas de gerenciamento do Active Directory local.

## O que vem a seguir
* [Adicionar um usuário](active-directory-users-create-azure-portal.md)
* [Redefinir a senha do usuário no novo Portal do Azure](active-directory-users-reset-password-azure-portal.md)
* [Atribuir um usuário a uma função no Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Alterar as informações de trabalho do usuário](active-directory-users-work-info-azure-portal.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Excluir um usuário no Azure AD](active-directory-users-delete-user-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->