<properties
	pageTitle="Criar um novo grupo na visualização do Azure Active Directory | Microsoft Azure"
	description="Como criar um grupo no Azure Active Directory e adicionar usuários (membros) ao grupo"
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


# Criar um novo grupo na visualização do Azure Active Directory

Este artigo explica como criar e popular um novo grupo na visualização do Azure Active Directory (Azure AD). [O que está na visualização?](active-directory-preview-explainer.md) Use um grupo para executar tarefas de gerenciamento, como a atribuição de licenças ou permissões a vários usuários ou dispositivos de uma vez.

## Como faço para criar um grupo?

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global para o diretório.

2. Selecione **Mais serviços**, digite **Usuário e grupos** na caixa de texto e, em seguida, selecione **Enter**.

  ![Abrindo o gerenciamento de usuários](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. Na folha **Usuários e grupos**, selecione **Todos os grupos**.

  ![Abrindo a folha de grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. Na folha **Usuários e grupos – Todos os grupos**, selecione o comando **Adicionar**.

  ![Selecionando o comando Adicionar](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. Na folha **Grupo**, adicione um nome e uma descrição ao grupo.

6. Para selecionar os membros a serem adicionados ao grupo, selecione **Atribuído** na caixa **Tipo de associação** e, em seguida, selecione **Membros**. Para obter mais informações sobre como gerenciar a associação de um grupo dinamicamente, consulte [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-users-azure-portal.md) ou [Gerenciar regras dinâmicas para dispositivos em um grupo](active-directory-groups-dynamic-devices-azure-portal.md).

  ![Selecionando membros para adicionar](./media/active-directory-groups-create-azure-portal/select-members.png)

5. Na folha **Membros**, selecione um ou mais usuários ou dispositivos para adicionar ao grupo e selecione o botão **Selecionar** na parte inferior da folha para adicioná-los ao grupo. A caixa **Usuário** filtra a exibição com base na correspondência de sua entrada com qualquer parte de um nome de usuário ou dispositivo. Caracteres curinga não são aceitos nessa caixa.

6. Quando terminar de adicionar membros ao grupo, selecione **Criar** na folha **Grupo**.

  ![Criar a confirmação do grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## Informações adicionais

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Consultar grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->