<properties 
	pageTitle="Registrar para autenticação no Google - Serviços Móveis" 
	description="Saiba como registrar seus aplicativos para usar o Google para autenticar com os Serviços Móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registrar seus aplicativos para logon no Google com os Serviços Móveis

Este tópico mostra a você como registrar seus aplicativos para poder usar o Google para se autenticar com os Serviços Móveis do Azure.

>[AZURE.NOTE] Este tutorial é sobre [Serviços Móveis do Azure](http://azure.microsoft.com/services/mobile-services/), uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilitam a sincronização de dados, autenticam usuários e enviam notificações por push. Esta página oferece suporte ao tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introdução à autenticação</a> que mostra como fazer o logon de usuários no seu aplicativo. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introdução aos Serviços Móveis</a>.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, entre com suas credenciais de conta do Google, clique em **Criar Projeto**, forneça um **Nome do projeto**, em seguida, clique em **Criar**.

   	![][1]

2. Clique em **Tela de consentimento**, selecione seu **Endereço de Email**, insira um **Nome do Produto**, em seguida, clique em **Salvar**. 

3. Clique em **API e Autenticação**, clique em **Credenciais**, depois clique em **Criar nova ID de Cliente**.

   	![][2]

4. Selecione **Aplicativo Web**, digite a URL de serviço móvel em **Origens de JavaScript Autorizadas**, substitua a URL gerada em **URI de Redirecionamento Autorizado** com a URL do seu serviço móvel anexada ao caminho _/login/google_e, em seguida, clique em **Criar ID do Cliente**.

	>[AZURE.NOTE] Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho _signin-google_ seu serviço móvel como um serviço .NET, por exemplo <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. Em **ID do Cliente para aplicativos web**, anote os valores da **ID do Cliente** e do **Segredo do cliente**. 

   	![][4]

    > [AZURE.IMPORTANT] O segredo do cliente é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

Agora você está pronto para usar um logon do Google para autenticação no seu aplicativo, fornecendo os valores de ID do cliente e de segredo do cliente para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Apis do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

\<!--HONumber=42-->
