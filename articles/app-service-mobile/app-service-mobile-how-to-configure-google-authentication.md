---
title: Como configurar a autenticação do Google para seu aplicativo de Serviços de Aplicativos
description: Saiba como configurar a autenticação do Google para seu aplicativo de Serviços de Aplicativos.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/22/2016
ms.author: mahender

---
# Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registre seu aplicativo com o Google
1. Faça logon no [portal do Azure] e navegue até o seu aplicativo. Copie a **URL**, pois você a usará mais tarde para configurar seu aplicativo do Google.
2. Vá até o site [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.
3. Em **APIs sociais**, clique em **API do Google+** e, então, em **Habilitar**.
4. No painel navegação à esquerda, clique em **Credenciais** > **Tela de consentimento de OAuth**, selecione seu **Endereço de email**, insira um **Nome do Produto** e clique em **Salvar**.
5. Na guia **Credenciais**, clique em **Criar credenciais** > **ID do Cliente OAuth** e, então, selecione **aplicativo Web**.
6. Cole a **URL** do Serviço de Aplicativo que você copiou anteriormente em **Origens Autorizadas do JavaScript** e, então, cole seu URI de Redirecionamento em **URI de Redirecionamento Autorizado**. O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho */.auth/login/google/callback*. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Certifique-se de que você está usando o esquema HTTPS. Em seguida, clique em **Criar**.
7. Na próxima tela, anote os valores de ID do cliente e de segredo do cliente.

    > [AZURE.IMPORTANT]
    O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo
1. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** e em **Autenticação / Autorização**.
2. Se o recurso Autenticação / Autorização não estiver habilitado, mude a opção para **Ativado**.
3. Clique em **Google**. Cole os valores de ID do Aplicativo e de Segredo do Aplicativo que você obteve anteriormente e, opcionalmente, habilite os escopos exigidos pelo seu aplicativo. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
4. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Google, defina **Ação a ser executada quando a solicitação não for autenticada** como **Google**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Google para autenticação.
5. Clique em **Salvar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0824_2016-->