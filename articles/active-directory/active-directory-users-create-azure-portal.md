<properties
	pageTitle="Adicionar novos usuários à visualização do Azure Active Directory | Microsoft Azure"
	description="Explica como adicionar novos usuários ou como alterar informações do usuário no Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Adicionar novos usuários à visualização do Azure Active Directory

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-users-create-azure-portal.md)
- [Portal clássico do Azure](active-directory-create-users.md)

Este artigo explica como adicionar novos usuários na sua organização na visualização do Azure AD (Azure Active Directory). [O que há na visualização?](active-directory-preview-explainer.md)

1.  Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

    ![Abrir o gerenciamento de usuários](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  Na folha **Usuários e grupos**, escolha **Todos os usuários** e **Adicionar**.

    ![Selecionando o comando Adicionar](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Insira os detalhes do usuário, como **Nome** e **Nome de usuário**. A parte do nome do domínio do nome do usuário deve ser o nome do domínio padrão inicial "foo.onmicrosoft.com", ou um nome de domínio verificado não federado, como "contoso.com".

5. Copie ou anote a senha de usuário gerada para que você possa fornecê-la ao usuário depois que esse processo estiver concluído.

6. Se preferir, você pode abrir e preencher as informações na folha **Perfil**, na folha **Grupos** ou na folha **Função do Directory** do usuário. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

7.  Na folha nova, **Usuário**, escolha **Criar**.

8. Distribua com segurança a senha gerada para o novo usuário para que ele possa entrar.

## O que vem a seguir

- [Adicionar um usuário externo](active-directory-users-create-external-azure-portal.md)
- [Redefinir a senha do usuário no novo Portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Alterar as informações de trabalho do usuário](active-directory-users-work-info-azure-portal.md)
- [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
- [Excluir um usuário no Azure AD](active-directory-users-delete-user-azure-portal.md)
- [Atribuir um usuário a uma função no Azure AD](active-directory-users-assign-role-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->