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
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.


> [AZURE.NOTE]
Este tópico demonstra o uso do recurso de Autenticação / Autorização do Serviço de Aplicativo. Isso substitui o gateway do Serviço de Aplicativo para a maioria dos aplicativos. As diferenças que se aplicam ao uso do gateway são destacadas nas notas ao longo desse tópico.


## <a name="register"> </a>Registre seu aplicativo com o Twitter


1. Faça logon no [portal do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar o seu aplicativo do Twitter.

2. Navegue até o site de [Desenvolvedores do Twitter,] entre com suas credenciais da conta do Twitter e clique em **Criar Novo Aplicativo**.

3. Digite o **Nome** e uma **Descrição** para o novo aplicativo. Cole a **URL** do aplicativo no valor **Site**. Em seguida, em **URL de Callback**, cole a **URL de Callback** copiada anteriormente. Esse é o seu gateway de Aplicativo Móvel, acrescentado com o caminho _/.auth/login/twitter/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que você está usando o esquema HTTPS.

	> [AZURE.NOTE]
	Se você estiver usando o Gateway do Serviço de Aplicativo em vez do recurso de Autenticação/Autorização do Serviço de Aplicativo, a URL de redirecionamento usará a URL de gateway com o caminho _/signin-twitter_.

3.  Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **Criar seu Aplicativo do Twitter**. Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

4. Clique na guia **Configurações**, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar Configurações**.

5. Selecione a guia **Chaves e Tokens de Acesso**. Tome nota dos valores da **Chave do Consumidor (Chave de API)** e **Segredo do consumidor (Segredo de API)**.

    > [AZURE.NOTE] O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.


## <a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo

> [AZURE.NOTE]
Se for usar o Gateway do Serviço de Aplicativo, ignore esta seção e navegue até o gateway no portal. Selecione **Configurações**, **Identidade**, e então **Twitter**. Cole os valores obtidos anteriormente e clique em **Salvar**.


13. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** e em **Autenticação / Autorização**.

14. Se o recurso Autenticação / Autorização não estiver habilitado, mude a opção para **Ativado**.

15. Clique em **Twitter**. Cole os valores de ID do aplicativo e segredo do aplicativo obtidos anteriormente. Em seguida, clique em **OK**.

    ![][1]

	Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.

17. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Twitter, defina **Ação a ser executada quando a solicitação não for autenticada** como **Twitter**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Twitter para autenticação.

17. Clique em **Salvar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=AcomDC_0211_2016-->