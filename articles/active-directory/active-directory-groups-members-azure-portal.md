<properties
	pageTitle="Gerenciar os membros de um grupo na visualização do Azure Active Directory | Microsoft Azure"
	description="Como gerenciar usuários e dispositivos que são membros de um grupo no Azure Active Directory"
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


# Gerenciar os membros de um grupo na visualização do Azure Active Directory

Este artigo explica como gerenciar os membros de um grupo na visualização do Azure AD (Azure Active Directory). [O que há na visualização?](active-directory-preview-explainer.md)

## Como localizo os membros e os gerencio?

1.  Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

  ![Abrir o gerenciamento de usuários](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Na folha **Usuários e grupos**, escolha **Todos os grupos**.

  ![Abrir a folha de grupos](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Na folha **Usuários e grupos - Todos os grupos**, escolha um grupo.

5. Na folha **Grupo - *nome do grupo***, escolha **Membros**.

  ![Abrir a folha Membros](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Para adicionar membros ao grupo, na folha **Grupo - Membros**, escolha **Adicionar Membros**.

  ![Comando Adicionar Membros](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Na folha **Membros**, escolha um ou mais usuários ou dispositivos para adicionar ao grupo e escolha o botão **Selecionar** na parte inferior da folha para adicioná-los ao grupo. A caixa **Usuário** filtra a exibição com base na correspondência de sua entrada com qualquer parte de um nome de usuário ou dispositivo. Caracteres curinga não são aceitos nessa caixa.

8. Para remover membros do grupo, na folha **Grupo - Membros**, escolha um membro.

9. Na folha ***nome do membro***, escolha o comando **Remover** e confirme sua opção na solicitação.

  ![Comando Remover Membros](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Quando terminar de alterar os membros do grupo, escolha **Salvar**.


## Informações adicionais

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->