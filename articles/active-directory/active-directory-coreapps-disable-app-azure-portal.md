---
title: "Desabilitar entradas de usuário em um aplicativo empresarial na visualização do Azure Active Directory | Microsoft Docs"
description: "Como desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: c57505d037d991f3c0d720a481393ec2ae6b8069
ms.openlocfilehash: ef6ae32c1d9b2e417d2a766207575f19aa06feaa


---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Desabilitar entradas de usuário em um aplicativo empresarial na visualização do Azure Active Directory
É fácil desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele na visualização do Azure AD (Azure Active Directory). [O que há na visualização?](active-directory-preview-explainer.md) Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial. Na visualização atual, você deve ser um administrador global do diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desabilitar entradas de usuário?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Azure Active Directory** na caixa de texto e selecione **Enter**.
3. Na folha **Azure Active Directory – ** -  ***nomedodiretório*** (ou seja, a folha do Azure AD para o diretório que você está gerenciando), escolha **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Na folha **Aplicativos empresariais**, escolha **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5. Na folha **Aplicativos empresariais – Todos os aplicativos** , escolha um aplicativo.
6. Na folha ***nomedoaplicativo*** (ou seja, a folha com o nome do aplicativo escolhido no título), escolha **Propriedades**.

    ![Seleção do comando todos os aplicativos](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Na folha ***nomedoaplicativo*** - ** Propriedades**, escolha **Não** para **Habilitado para usuários entrarem?**.
8. Escolha o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)



<!--HONumber=Jan17_HO3-->


