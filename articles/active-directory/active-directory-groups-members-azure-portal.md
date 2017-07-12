---

title: Gerenciar os membros de um grupo no Azure Active Directory | Microsoft Docs
description: "Como adicionar ou remover usuários e dispositivos de um grupo no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 044e88f95712e1cc5b5532f5492c78d711a8d858
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017


---
<a id="manage-group-membership-for-users-in-your-azure-active-directory-tenant" class="xliff"></a>

# Gerenciar associação de grupo de usuários em seu locatário do Azure Active Directory
Este artigo explica como gerenciar os membros de um grupo no Azure AD (Azure Active Directory).

<a id="how-do-i-find-the-members-and-manage-them" class="xliff"></a>

## Como localizo os membros e os gerencio?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

   ![Abrir o gerenciamento de usuários](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. Na folha **Usuários e grupos**, escolha **Todos os grupos**.

   ![Abrir a folha de grupos](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Na folha **Usuários e grupos - Todos os grupos** , escolha um grupo.
5. Na folha **Grupo – *nomedogrupo*** , selecione **Membros**.

   ![Abrir a folha Membros](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Para adicionar membros ao grupo, na folha **Grupo - Membros**, selecione **Adicionar Membros**.

   ![Comando Adicionar Membros](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. Na folha **Membros**, escolha um ou mais usuários ou dispositivos para adicionar ao grupo e escolha o botão **Selecionar** na parte inferior da folha para adicioná-los ao grupo. A caixa **Usuário** filtra a exibição com base na correspondência de sua entrada com qualquer parte de um nome de usuário ou dispositivo. Caracteres curinga não são aceitos nessa caixa.
8. Para remover membros do grupo, na folha **Grupo - Membros** , escolha um membro.
9. Na folha ***membername***, selecione o comando **Remover** e confirme sua escolha no prompt.

   ![Comando Remover Membros](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Quando terminar de alterar os membros do grupo, escolha **Salvar**.

<a id="additional-information" class="xliff"></a>

## Informações adicionais
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Ver grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)

