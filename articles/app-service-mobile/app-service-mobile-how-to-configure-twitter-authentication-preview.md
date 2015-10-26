<properties
	pageTitle="Como configurar a autenticação do Twitter para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Twitter para seu aplicativo de Serviços de Aplicativos."
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

# Como configurar seu aplicativo para usar o logon do Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como configurar os aplicativos móveis do Azure para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registre seu aplicativo com o Twitter


1. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o seu aplicativo móvel. Copie a sua **URL**. Você a usará para configurar o seu aplicativo do Twitter.

2. Clique em **Configurações**, **Autenticação de usuário**, e, em seguida, clique em **Twitter**. Copie a **URL de callback**. Você a usará para configurar o seu aplicativo do Twitter.

3. Navegue até o site de [Desenvolvedores do Twitter,] entre com suas credenciais da conta do Twitter e clique em **Criar Novo Aplicativo**.

4. Digite o **Nome** e uma **Descrição** para seu novo aplicativo. Cole a **URL do aplicativo móvel** no valor do **Website**. Em seguida, em **URL de callback**, cole a **URL de callback** copiada anteriormente. Esse é o seu gateway de aplicativo móvel acrescentado do caminho _/signin-twitter_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-twitter`. Certifique-se de que você está usando o esquema HTTPS.

    ![][0]

3.  Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **Criar seu Aplicativo do Twitter**. Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

4. Clique na guia **Configurações**, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar Configurações**.

5. Selecione a guia **Chaves e Tokens de Acesso**. Tome nota dos valores da **Chave do Consumidor (Chave de API)** e **Segredo do consumidor (Segredo de API)**.

    > [AZURE.NOTE]O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.


## <a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo móvel

1. No [Portal de gerenciamento], na folha de configurações do Twitter para seu aplicativo móvel, cole os valores de Chave de API e Segredo de API que você obteve anteriormente. Em seguida, clique em **Salvar**.

    ![][1]

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de Gerenciamento do Azure]: https://portal.azure.com/
[Portal de gerenciamento]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=Oct15_HO3-->