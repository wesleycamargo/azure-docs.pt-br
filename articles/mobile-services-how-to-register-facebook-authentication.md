<properties 
	pageTitle="Registrar para autenticação no Twitter - Serviços Móveis" 
	description="Aprenda a usar a autenticação do Facebook em seu aplicativo de Serviços Móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registrar seus aplicativos para a autenticação do Facebook com os Serviços Móveis

Este tópico mostra como registrar seus aplicativos para poder usar o Facebook para autenticar com os Serviços Móveis do Azure. 

> [AZURE.NOTE] Este tutorial é sobre [Serviços Móveis do Azure], uma solução para ajudá-lo a criar aplicativos móveis escalonáveis para qualquer plataforma. Os Serviços Móveis facilitam a sincronização de dados, autenticam usuários e enviam notificações por push. Esta página dá suporte ao tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Introdução à autenticação</a> que mostra como registrar usuários em seu aplicativo. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Introdução aos Serviços Móveis</a>.
	
Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook com um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Navegue até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Desenvolvedores do Facebook</a> e entre com as credenciais de sua conta do Facebook.

2. (Opcional) Se você ainda não foi registrado, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro. 

   	![][0]

3. Clique em **Aplicativos** e clique em **Criar um Novo Aplicativo**.

   	![][1]

4. Escolha um nome exclusivo para seu aplicativo, selecione **Aplicativos para Páginas**, clique em **Criar Aplicativo** e conclua a pergunta do desafio.

   	![][2]

	Isso registra o aplicativo com o Facebook 

5. Clique em **Configurações**, digite o domínio do seu serviço móvel em **Domínios de Aplicativo**. Insira também um **Email de Contato**, em seguida, clique em **Adicionar Plataforma** e selecione **Site**.

   	![][3]

6. Digite a URL do seu serviço móvel em **URL do Site** e clique em **Salvar Alterações**.

	![][4]

7. Clique em **Mostrar**, forneça sua senha se solicitado e anote os valores de **ID do Aplicativo** e **Segredo do Aplicativo**. 

   	![][5]

	> [AZURE.NOTE] **Observação de segurança**
	O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.


8. Clique na guia **Avançado**, digite a URL de seu serviço móvel acrescentada ao caminho _/login/facebook_ em **URIs de redirecionamento do OAuth válido** e clique em **Salvar alterações**. 

	> [AZURE.NOTE] Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho _signin-facebook_ e o seu serviço móvel é um serviço .NET, por exemplo <code>https://todolist.azure-mobile.net/signin-facebook</code>.  
	
	![][7]

9. A conta do Facebook, para o qual você definiu o novo aplicativo, é um administrador do aplicativo e tem acesso ao aplicativo como administrador. Para autenticar outras contas do Facebook, eles precisam ter acesso ao aplicativo. Esta etapa concede acesso ao público geral para que o aplicativo possa autenticar outras contas do Facebook. Clique em **Status e Revisão**. Em seguida, clique em **Sim** para habilitar o acesso público geral.

    ![][6]



Agora você está pronto para usar um logon do Facebook para autenticação em seu aplicativo, fornecendo os valores de ID do Aplicativo e Segredo do Aplicativo para os Serviços Móveis.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Desenvolvedores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Introdução à autenticação]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Serviços móveis do Azure]: http://azure.microsoft.com/services/mobile-services/

<!--HONumber=47-->
