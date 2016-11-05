---
title: Desabilitar entradas de usuário em um aplicativo empresarial na visualização do Azure Active Directory | Microsoft Docs
description: Como desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory
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
# Desabilitar entradas de usuário em um aplicativo empresarial na visualização do Azure Active Directory
É fácil desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele na visualização do Azure AD (Azure Active Directory). [O que há na visualização?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial. Na visualização atual, você deve ser um administrador global do diretório.

## Como desabilitar entradas de usuário?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Azure Active Directory** na caixa de texto e selecione **Enter**.
3. Na folha **Azure Active Directory – *nomedodiretório*** (ou seja, a folha do Azure AD para o diretório que você está gerenciando), escolha **Aplicativos empresariais**.
   
    ![Abrir aplicativos empresariais](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Na folha **Aplicativos empresariais**, escolha **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5. Na folha **Aplicativos empresariais – Todos os aplicativos**, escolha um aplicativo.
6. Na folha ***nomedoaplicativo*** (ou seja, a folha com o nome do aplicativo escolhido no título), escolha **Propriedades**.
   
    ![Seleção do comando todos os aplicativos](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Na folha ***nomedoaplicativo*** **- Propriedades**, escolha **Não** para **Habilitado para usuários entrarem?**.
8. Escolha o comando **Salvar**.

## Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-user-azure-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->