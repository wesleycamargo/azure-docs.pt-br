<properties 
	pageTitle="Registrar para autenticação no Twitter - Serviços Móveis" 
	description="Aprenda a usar a autenticação do Twitter com o aplicativo de serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

#Registrar seus aplicativos para logon do Twitter com os Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Este tópico mostra a você como registrar seus aplicativos para usar o Twitter e se autenticar com os Serviços Móveis do Azure.

>[AZURE.NOTE]Este tutorial é sobre os [Serviços Móveis do Azure](http://azure.microsoft.com/services/mobile-services/), uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilita a sincronização de dados, autentica usuários e envia notificações por push. Esta página oferece suporte ao tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introdução à autenticação</a>, que mostra como fazer o logon de usuários no seu aplicativo. Se essa for sua primeira experiência com os Serviços Móveis, conclua o tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introdução aos Serviços Móveis</a>.

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Navegue até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Desenvolvedores do Twitter</a>, entre com suas credenciais de conta do Twitter e clique em **Criar um novo aplicativo**.

   	![][1]

2. Digite os valores **Nome**, **Descrição** e **Site** para seu aplicativo, depois digite a URL para seu serviço acrescentado ao caminho _/login/twitter_ em **URL de Retorno de Chamada**.

	>[AZURE.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho _signin-twitter_ e o seu serviço móvel é um serviço .NET, por exemplo <code>https://todolist.azure-mobile.net/signin-twitter</code>.

   	![][2]

3.  Na parte inferior da página, leia e aceite os termos, digite as palavras corretas de CAPTCHA e, em seguida, clique em **Criar seu aplicativo Twitter**.

   	![][3]

   	Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

6. Anote os valores de **Chave do consumidor** e **Segredo do consumidor**.

   	![][4]

    > [AZURE.NOTE]O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

7. Clique na guia **Configurações**, role para baixo, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar as configurações deste aplicativo Twitter**.

	![][5]

Agora você está pronto para usar um logon do Twitter para autenticação no seu aplicativo, fornecendo os valores de chave do consumidor e de segredo do consumidor para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->