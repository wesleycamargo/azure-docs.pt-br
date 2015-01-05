<properties pageTitle="Registrar-se para logon único - Serviços Móveis do Azure" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrar seus aplicativos da Windows Store para usar o logon único no Windows Live Connect

Este tópico mostra como registrar seu aplicativo da Windows Store para usar o logon único do Live Connect como o provedor de identidade dos Serviços Móveis do Azure. Esta etapa também é necessária para usar notificações por push.

<div class="dev-callout"><b>Observação</b>
<p>Não é necessário registrar seu aplicativo da Windows Store para usar a Conta da Microsoft para autenticação antes de publicar seu aplicativo. Quando seu aplicativo da Windows Store não exigir logon único para notificações por push, você pode registrá-lo no Live Connect para usar um logon de Conta da Microsoft.  Para obter mais informações, consulte <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication">Registrar seus aplicativos da Windows Store para usar um logon de Conta da Microsoft</a>.</p>
</div>

1. Se você não tiver registrado seu aplicativo, navegue até [Enviar uma página do aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua Conta da Microsoft e, em seguida, clique em **Nome do Aplicativo**.

   	![][0]

2. Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![][1]

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em**Loja**, e clique em **Associar Aplicativo com a Loja...**. 

  	![][2]

   	Isso exibe o Assistente de **Associar o Aplicativo com a Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Avançar**, e depois clique em **Associar**.

   	![][3]

   	Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.    

9. Navegue até a página [Meus Aplicativos] na Central de Desenvolvedores do Live Connect e clique em seu aplicativo na lista**Meus aplicativos**.

   	![][6] 

10. Clique na página **Editar configurações**, em seguida, em **Configurações da API** e anote o valor de **Segredo do cliente**. 

   	![][7]

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem o distribua com seu aplicativo.</p>
    </div>

11. Em **Domínio de redirecionamento**, digite a URL do seu serviço móvel da Etapa 8 e, em seguida, clique em **Salvar**.

Agora você está pronto para integrar a autenticação usando o Live Connect em seu aplicativo. Os Serviços Móveis fornecem os dois seguintes métodos para autenticar usuários usando o Live Connect:

   - Logon único para aplicativos da Windows Store. Nesse método, os usuários só precisam autorizar a autenticação em seu aplicativo uma vez usando o Live Connect e, em seguida, as credenciais são gerenciadas pelo Windows, com base nas preferências do usuário. Para obter mais informações, consulte [Logon único para aplicativos da Windows Store usando o Live Connect].

   - Autenticação básica. Esse método, que dá suporte a vários provedores de autenticação, requer que os usuários façam logon sempre que seu aplicativo é iniciado. Para obter mais informações, consulte [Comece a usar a autenticação].

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
