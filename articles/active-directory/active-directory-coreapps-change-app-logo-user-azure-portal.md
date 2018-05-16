---
title: Alterar o nome ou logotipo de um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: a8c166e4c0abeddbdb23622e147cd7ecd5395b05
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Alterar o nome ou logotipo de um aplicativo empresarial no Azure Active Directory
É fácil alterar o nome ou logotipo de um aplicativo empresarial personalizado no Azure AD (Azure Active Directory). Você deve ter as permissões apropriadas para fazer essas alterações, além de ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Como alterar o nome ou logotipo de um aplicativo empresarial?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3. No painel **Azure Active Directory - *nomedodiretório*** (ou seja, o painel do Azure Active Directory para o diretório que você está gerenciando), selecione **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. No painel **Aplicativos empresariais**, selecione **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5. No painel **Aplicativos empresariais – Todos os aplicativos**, selecione um aplicativo.
6. No painel ***nomedoaplicativo*** (ou seja, o painel com o nome do aplicativo selecionado no título), selecione **Propriedades**.

    ![Seleção do comando de propriedades](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. No painel ***nomedoaplicativo*** **- Propriedades**, procure um arquivo para usar como um novo logotipo ou edite o nome do aplicativo, ou ambos.

    ![Alteração do logotipo do aplicativo ou do comando nameproperties](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Escolha o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Desabilitar as entradas de usuário em um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)
