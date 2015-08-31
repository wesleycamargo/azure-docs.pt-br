<properties
	pageTitle="Como configurar a autenticação do Google para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Google para seu aplicativo de Serviços de Aplicativos."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo para usar o logon do Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como configurar aplicativos móveis para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Registre seu aplicativo com o Google


1. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o seu aplicativo móvel. Copie a sua **URL**. Você a usará posteriormente com seu aplicativo Google.
 
2. Clique em **Configurações**, **Autenticação de usuário**, e, em seguida, clique em **Google**. Copie o **URI de Redirecionamento**. Você o usará para configurar o seu aplicativo Google.

3. Vá até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

4. Na barra de navegação à esquerda, clique em **API e Autenticação**. Em seguida, clique na **Tela de consentimento**. Selecione seu **Endereço de Email** e insira um **Nome do produto**. Em seguida, clique em **Salvar**.

5. Também em **API e Autenticação**, selecione **APIs** e habilite a **API do Google +**. Está localizado em **APIs sociais**. Você também pode procurar por **API do Google+**.

6. Mais uma vez, em **API e Autenticação**, selecione **Credenciais** e **Criar nova ID de Cliente**.

7. Selecione **Aplicativo Web**. Cole a **URL** você copiou anteriormente em **Origens de JavaScript autorizadas**, e, em seguida, substitua a URL gerada em **URI de redirecionamento autorizado** pelo **URI de redirecionamento** do aplicativo móvel copiado anteriormente. Esse URI é o gateway de aplicativo móvel acrescentado do caminho _/signin-google_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-google`. Certifique-se de que você está usando o esquema HTTPS. Em seguida, clique em **Criar ID do cliente**.

     ![][0]

8. Na próxima tela, em **ID do Cliente para aplicativos web**, anote os valores da **ID do Cliente** e do **Segredo do cliente**.

    > [AZURE.IMPORTANT]O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações do Google ao aplicativo móvel

7. No [Portal de gerenciamento], na folha de configurações do Google para seu aplicativo móvel, cole os valores de ID do cliente e segredo do cliente que você obteve anteriormente. Em seguida, clique em **Salvar**.

     ![][1]


Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Gerenciamento do Azure]: https://portal.azure.com/
[Portal de gerenciamento]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->