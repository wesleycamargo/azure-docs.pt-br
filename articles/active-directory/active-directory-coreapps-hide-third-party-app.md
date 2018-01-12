---
title: "Ocultar um aplicativo da experiência do usuário no Azure Active Directory | Microsoft Docs"
description: "Como ocultar um aplicativo da experiência do usuário no Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 667fdd45bc9eb1f01ce3883006bb29274478cb83
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Ocultar um aplicativo da experiência do usuário no Azure Active Directory

Se você tiver um aplicativo que você não deseje que apareça nos painéis de acesso dos usuários nem em iniciadores do Office 365, há uma opção para ocultar esse bloco de aplicativo. Essa opção só está disponível para aplicativos de terceiros (aplicativos não publicados pela Microsoft). Ao ocultar o aplicativo, os usuários ainda têm permissões para o aplicativo, mas não os verão no inicializador de aplicativo. Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório. 

## <a name="hiding-an-application-from-users-end-user-experiences"></a>Ocultando um aplicativo com experiências do usuário final
Use as seguintes etapas para ocultar um aplicativo do painel de acesso de um usuário e dos inicializadores de aplicativo do Office 365

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Como fazer para ocultar um aplicativo de terceiros do painel de acesso do usuário e dos inicializadores de aplicativo do O365?

1.  Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2.  Escolha **Mais serviços**, insira **Azure Active Directory** na caixa de texto e selecione **Enter**.
3.  Na tela **Azure Active Directory – *nomedodiretório*** (ou seja, a tela do Azure AD para o diretório que você está gerenciando), escolha **Aplicativos Empresariais**.
![Aplicativos empresariais](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Na tela **Aplicativos empresariais**, escolha **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5.  Na tela **Aplicativos empresariais – todos os aplicativos**, selecione um aplicativo.</br>
![Aplicativos empresariais](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Na tela ***nomedoaplicativo*** (ou seja, a tela com o nome do aplicativo escolhido no título), selecione Propriedades.
7.  Na tela ***nomedoaplicativo* – propriedades**, selecione **Sim** para **Visível para os usuários?**.
![Aplicativos empresariais](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Escolha o comando **Salvar** .

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
