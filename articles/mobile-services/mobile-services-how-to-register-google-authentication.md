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
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Registrar seus aplicativos para logon no Google com os Serviços Móveis

Este tópico mostra a você como registrar seus aplicativos para poder usar o Google para se autenticar com os Serviços Móveis do Azure.

>[AZURE.NOTE]Este tutorial é sobre os [Serviços Móveis do Azure](http://azure.microsoft.com/services/mobile-services/), uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilita a sincronização de dados, autentica usuários e envia notificações por push. Esta página oferece suporte ao tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introdução à autenticação</a>, que mostra como fazer o logon de usuários no seu aplicativo. Se essa for sua primeira experiência com os Serviços Móveis, conclua o tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introdução aos Serviços Móveis</a>.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Vá até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

   	![][1]

2. Clique em **Tela de consentimento**, selecione seu **Endereço de Email**, insira um **Nome do Produto** e clique em **Salvar**.

3. Clique em **API & Auth**, clique em **Credenciais** e clique em **Criar nova ID de cliente**.

   	![][2]

4. Selecione **Aplicativo Web**, digite a URL do seu Serviço Móvel em **Authorized JavaScript Origins**, substitua a URL gerada em **URI de Redirecionamento Autorizados** com a URL do seu serviço móvel acrescentado ao caminho _/login/google_ e clique em **Criar ID do cliente**.

	>[AZURE.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho _signin-google_ e o seu serviço móvel é um serviço .NET, por exemplo <code>https://todolist.azure-mobile.net/signin-google</code>.

   	![][3]

5. Em **ID do Cliente para aplicativos web**, anote os valores da **ID do Cliente** e do **Segredo do cliente**.

   	![][4]

    > [AZURE.IMPORTANT]O segredo do cliente é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

Agora você está pronto para usar um logon do Google para autenticação no seu aplicativo, fornecendo os valores de ID do cliente e de segredo do cliente para os Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54--> 