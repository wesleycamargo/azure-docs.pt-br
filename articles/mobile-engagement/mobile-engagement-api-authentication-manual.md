---
title: "Autenticar com APIs REST do Mobile Engagement: configuração manual"
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
ms.openlocfilehash: 0b4a999c6778040e71f862d3a010b6635e84b26e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis-manual-setup"></a>Autenticar com APIs REST do Mobile Engagement: configuração manual
Esta documentação é um apêndice para [Autenticar com APIs REST do Mobile Engagement](mobile-engagement-api-authentication.md). Primeiro, leia esse artigo para entender o contexto. Este artigo também descreve uma maneira alternativa de fazer a configuração de autenticação única para as APIs REST do Mobile Engagement usando o Portal do Azure.

> [!NOTE]
> As seguintes instruções são baseadas neste [guia do Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Elas são personalizadas para os requisitos de autenticação das APIS do Mobile Engagement. Consulte-o, para entender as etapas abaixo em detalhes.

1. Entre sua conta do Azure através do [Portal do Azure](https://portal.azure.com/).
2. Selecione **Active Directory** no painel à esquerda.

   ![Selecionar o Microsoft Azure AD][1]

3. Para exibir os aplicativos em seu diretório, selecione **Registros de aplicativo**.

   ![Exibir aplicativos][3]

4. Selecione **Novo registro de aplicativo**.

   ![Adicionar aplicativo][4]

5. Preencha o nome do aplicativo. Deixe o tipo do aplicativo como **Aplicativo Web/API**e, em seguida, selecione o botão **Próximo**. Você pode fornecer qualquer URL fictícia para a **URL DE ENTRADA**. Eles não são utilizadas para esse cenário e as próprias URLs não são validadas.

   Após terminar, você terá um aplicativo Azure AD (Azure Active Directory) com o nome fornecido. Este é o seu **NOME DO\_APLICATIVO\_DO AD**, então, não deixe de anotá-lo.

   ![Nome do aplicativo][8]

7. Selecione o nome do aplicativo.

8. Localize a **ID do Aplicativo** e anote-o. Essa é a ID do cliente que será utilizada como **ID\_DO CLIENTE** para as chamadas de API.

   ![Localizar a ID do aplicativo][10]

9. Localize a seção **Chaves** à direita.

   ![Seção Chaves][11]

10. Crie uma nova chave e, em seguida, copie-a imediatamente. Ela não será mostrada novamente.

    ![Painel de chaves com detalhes de chave][12]

    > [!IMPORTANT]
    > Essa chave expira no término da duração especificada. Certifique-se de renová-lo quando chegar a hora, caso contrário, a sua autenticação da API não funcionará mais. Se você suspeitar que essa chave foi comprometida, será possível excluí-la e recriá-la.
    >
    
11. Selecione o botão **Pontos de extremidade** na parte superior da página. Em seguida, copie **PONTO DE EXTREMIDADE DO TOKEN OAUTH 2.0**.

    ![Copiar o ponto de extremidade][14]

16. Esse ponto de extremidade está no formulário seguinte, onde o GUID na URL é **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Em seguida, as permissões serão configuradas nesse aplicativo. Para iniciar o processo, vá para o [Portal do Azure](https://portal.azure.com).

18. Selecione **Grupo de recursos** e, em seguida, localize o grupo de recursos **MobileEngagement**.

    ![Localizar MobileEngagement][15]

19. Selecione o grupo de recursos **MobileEngagement**e, em seguida, selecione **Todas as configurações**.

    ![Procure as configurações do MobileEngagement][16]

20. Selecione **Usuários** na seção**Configurações**. Em seguida, para adicionar um usuário, selecione **Adicionar**.

    ![Adicionar um usuário][17]

21. Clique em **Selecionar uma Função**.

    ![Selecione uma função][18]

22. Selecione **Proprietário**.

    ![Selecione Proprietário como a função][19]

23. Procure o nome do seu aplicativo, **NOME DO\_APLICATIVO DO\_AD**, na caixa de pesquisa. Esse nome não está aqui por padrão. Depois de localizá-lo, selecione-o. Em seguida, clique em **Selecionar** na parte inferior da seção.

    ![Selecionar o nome][20]

24. Na seção **Adicionar aceso**, ele aparece como **1 usuário, 0 grupos**. Para confirmar a alteração, selecione **OK**.

    ![Confirmar o usuário adicionado][21]

Você concluiu a configuração necessária do Microsoft Azure AD e está pronto para chamar as APIs.

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



