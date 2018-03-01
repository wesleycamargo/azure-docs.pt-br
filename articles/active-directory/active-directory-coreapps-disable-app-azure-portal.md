---
title: "Desabilitar entradas de usuário em um aplicativo empresarial no Azure Active Directory | Microsoft Docs"
description: "Como desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: e044d32406236aacaf7fffa2b4b19dadd96a7d5d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desabilitar entradas de usuário em um aplicativo empresarial no Azure Active Directory
É fácil desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure AD (Azure Active Directory). Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desabilitar entradas de usuário?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3. No painel **Azure Active Directory** -  ***nomedodiretório*** (ou seja, o painel do Azure Active Directory para o diretório que você está gerenciando), selecione **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. No painel **Aplicativos empresariais**, selecione **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5. No painel **Aplicativos empresariais – Todos os aplicativos**, selecione um aplicativo.
6. No painel ***nomedoaplicativo*** (ou seja, o painel com o nome do aplicativo selecionado no título), selecione **Propriedades**.

    ![Seleção do comando todos os aplicativos](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. No painel ***nomedoaplicativo*** -  **Propriedades**, selecione **Não** para **Habilitar para os usuários entrarem?**.
8. Escolha o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
