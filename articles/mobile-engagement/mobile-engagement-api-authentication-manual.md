---
title: "Autenticar com APIs REST do Mobile Engagement - configuração manual"
description: "Descreve como configurar manualmente a autenticação para APIs REST do Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticar com APIs REST do Mobile Engagement - configuração manual
Esta é uma documentação de apêndice para se [Autenticar com APIs REST do Mobile Engagement](mobile-engagement-api-authentication.md). Leia esse artigo primeiro para obter o contexto.
Ele descreve uma maneira alternativa de fazer a configuração única da autenticação para APIs REST do Mobile Engagement usando o portal do Azure.

> [!NOTE]
> As instruções a seguir se baseiam neste [Guia do Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) e são personalizadas para o que é necessário para a autenticação de APIs do Mobile Engagement. Portanto, consulte-o para entender as etapas abaixo em detalhes.

1. Entre na sua conta do Azure por meio do [Portal do Azure](https://portal.azure.com/).
2. Selecione **Active Directory** no painel à esquerda.

     ![selecionar Active Directory][1]

3. Para exibir os aplicativos em seu diretório, clique em **Registros de aplicativo**.

     ![exibir aplicativos][3]

4. Clique em **Novo registro de aplicativo**.

     ![Adicionar aplicativo][4]

5. Preencha o nome do aplicativo e selecione o tipo de aplicativo como **Aplicativo Web/API** e clique no botão Avançar. Você pode fornecer quaisquer URLs fictícias para a **URL DE LOGON**: elas não são usadas para este cenário e as URLs em si não são validadas.
6. Quando terminar, você terá um aplicativo Azure AD com o nome fornecido. Este é o seu **NOME DO\_APLICATIVO\_DO AD**. Anote-o.

     ![nome do aplicativo][8]

7. Clique no nome do aplicativo.
8. Localize a **ID do aplicativo**, anote-a, é a ID do CLIENTE que será usada como **ID DO\_CLIENTE** para suas chamadas de API.

     ![configurar aplicativo][10]

9. Localize a seção **Chaves** à direita.

     ![configurar aplicativo][11]

10. Crie uma nova chave e imediatamente copie e salve-a para uso futuro. Ela não aparecerá novamente.

     ![configurar aplicativo][12]

    > [!IMPORTANT]
    > Essa chave expirará ao final da duração especificada, portanto lembre-se de renová-la quando chegar a hora, caso contrário a autenticação da API não funcionará mais. Você também pode excluir e recriar essa chave se achar que ela foi comprometida.
    >
    >
11. Clique no botão **Pontos de extremidade** na parte superior da página e copie o **PONTO DE EXTREMIDADE DO TOKEN OAUTH 2.0**.

    ![][14]

16. Esse ponto de extremidade terá o seguinte formato, em que o GUID na URL é seu **TENANT_ID**, então anote-o: `https://login.microsoftonline.com/<GUID>/oauth2/token`
17. Agora, configuraremos as permissões nesse aplicativo. Para isso, você precisará abrir o [portal do Azure](https://portal.azure.com). 
18. Clique em **Grupos de Recursos** e localize o grupo de recursos **Mobile Engagement**.

    ![][15]

19. Clique no grupo de recursos **Mobile Engagement** e navegue até a seção **Configurações**.

    ![][16]

20. Clique em **Usuários** na seção Configurações e clique em **Adicionar** para adicionar um usuário.

    ![][17]

21. Clique em **Selecionar uma função**.

    ![][18]

22. Clique em **Proprietário**.

    ![][19]

23. Procure o nome de seu aplicativo **NOME\_APLICATIVO\_AD** na caixa Pesquisar. Você não verá ele por padrão aqui. Depois de encontrá-lo, selecione-o e clique em **Selecionar** na parte inferior da seção.

    ![][20]

24. Na seção **Adicionar acesso**, ele será exibido como **1 usuário, 0 grupos**. Clique em **OK** nessa seção para confirmar a alteração.

    ![][21]

Agora você concluiu a configuração necessária do Azure AD e está pronto para chamar as APIs.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



