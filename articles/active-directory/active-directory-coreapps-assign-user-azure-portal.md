---
title: Atribuir um usuário ou um grupo a um aplicativo empresarial na visualização do Azure Active Directory | Microsoft Docs
description: Como selecionar um aplicativo empresarial par atribuir um usuário ou um grupo a ele no Azure Active Directory
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
# Atribuir um usuário ou um grupo a um aplicativo empresarial na visualização do Azure Active Directory
É fácil atribuir um usuário ou um grupo aos aplicativos empresariais na visualização do Azure Active Directory (Azure AD). [O que está na visualização?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial. Na visualização atual, você deve ser um administrador global do diretório.

## Como atribuir acesso de usuário a um aplicativo empresarial?
1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Mais serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
3. Na folha **Azure Active Directory – *directoryname*** (ou seja, a folha do Azure AD para o diretório que você está gerenciando), selecione **Aplicativos Empresariais**.
   
   ![Abrindo aplicativos empresariais](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Na folha **Aplicativos empresariais**, selecione **Todos os aplicativos**. Você encontrará uma lista dos aplicativos que você pode gerenciar.
5. Na folha **Aplicativos empresariais – Todos os aplicativos**, selecione um aplicativo.
6. Na folha ***appname*** (ou seja, a folha com o nome do aplicativo selecionado no título), selecione **Usuários e Grupos**.
   
   ![Selecionando o comando Todos os aplicativos](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Na folha ***appname*** **- Atribuição de Usuário e Grupo**, selecione o comando **Adicionar**.
8. Na folha **Adicionar Atribuição**, selecione **Usuários e Grupos**.
   
   ![Atribuir um usuário ou um grupo ao aplicativo](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Na folha **Usuários e grupos**, selecione um ou mais usuários ou grupos na lista e, em seguida, selecione o botão **Selecionar** na parte inferior da folha.
10. Na folha **Adicionar Atribuição**, selecione **Função**. Então, na folha **Selecionar Função**, selecione uma função para aplicar aos usuários e grupos selecionados e selecione o botão **OK** na parte inferior da folha.
11. Na folha **Adicionar Atribuição**, selecione o botão **Atribuir** na parte inferior da folha. Os usuários ou os grupos atribuídos terão as permissões definidas pela função selecionada para esse aplicativo empresarial.

## Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-user-azure-portal.md)
* [Desabilitar logons de usuário para um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->