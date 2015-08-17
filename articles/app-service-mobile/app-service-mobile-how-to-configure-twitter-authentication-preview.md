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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo para usar o logon do Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como configurar os Serviços de Aplicativos do Azure para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registre seu aplicativo com o Twitter

1. Navegue até o site de [Desenvolvedores do Twitter,] entre com suas credenciais da conta do Twitter e clique em **Criar Novo Aplicativo**.

2. Digite os valores de **Nome**, **Descrição** e **Site da Web** para seu aplicativo. Em seguida, em **URL de Retorno de Chamada**, digite a URL do seu gateway anexada ao caminho, _/signin-twitter_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-twitter`. Certifique-se de que você está usando o esquema HTTPS.

    ![][0]

3.  Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **Criar seu Aplicativo do Twitter**. Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

4. Clique na guia **Configurações**, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar Configurações**.

5. Selecione a guia **Chaves e Tokens de Acesso**. Tome nota dos valores da **Chave do Consumidor (Chave de API)** e **Segredo do consumidor (Segredo de API)**.

    > [AZURE.NOTE]O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.


## <a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo móvel

6. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o gateway de Serviço de Aplicativo.

7. Em **Configurações**, escolha **Identidade** e, em seguida, selecione **Twitter**. Cole os valores de ID do aplicativo e segredo do aplicativo obtidos anteriormente. Em seguida, clique em **Salvar**.

    ![][1]

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de Gerenciamento do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=August15_HO6-->