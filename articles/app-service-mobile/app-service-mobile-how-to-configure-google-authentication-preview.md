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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo para usar o logon do Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como configurar Serviços de Aplicativos do Azure para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Registre seu aplicativo com o Google

1. Vá até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

2. Na barra de navegação à esquerda, clique em **API e Autenticação**. Em seguida, clique na **Tela de consentimento**. Selecione seu **Endereço de Email** e insira um **Nome do produto**. Em seguida, clique em **Salvar**.

3. Também em **API e Autenticação**, selecione **APIs** e habilite a **API do Google +**.

4. Mais uma vez, em **API e Autenticação**, selecione **Credenciais** e **Criar nova ID de Cliente**.

5. Selecione **Aplicativo Web**. Digite sua URL de gateway de **Serviços de Aplicativos em Origens JavaScript Autorizadas** e, em seguida, substitua a URL gerada em **URI de Redirecionamento Autorizado** pela URL do seu gateway anexada ao caminho, _/signin-google_. Por exemplo: `https://contoso.azurewebsites.net/signin-google`. Certifique-se de que você está usando o esquema HTTPS. Em seguida, clique em **Criar ID do cliente**.

     ![][0]

6. Na próxima tela, em **ID do Cliente para aplicativos web**, anote os valores da **ID do Cliente** e do **Segredo do cliente**.

    > [AZURE.IMPORTANT]O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações do Google ao aplicativo móvel

7. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o gateway de Serviços de Aplicativos.

8. Em **Configurações**, escolha **Identidade** e, em seguida, selecione **Google**. Cole os valores de ID do Cliente e Segredo do Cliente que obteve anteriormente. Em seguida, clique em **Salvar**.

     ![][1]


Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Gerenciamento do Azure]: https://portal.azure.com/
 

<!---HONumber=July15_HO4-->