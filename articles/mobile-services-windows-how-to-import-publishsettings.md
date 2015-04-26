<properties 
	pageTitle="Importar seu arquivo de configurações de publicação no Visual Studio 2013 | Serviços Móveis" 
	description="Saiba como importar um arquivo de configurações de publicação de assinatura para seu aplicativo de serviços móveis do Azure no Visual Studio 2013." 
	documentationCenter="windows" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Importar seu arquivo de configurações de assinatura no Visual Studio 2013

Antes de criar o serviço móvel, você deve importar o arquivo de configurações de publicação de sua assinatura do Azure no Visual Studio. Isso permite que o Visual Studio se conecte ao Azure em seu nome.  

>[AZURE.NOTE] A partir do Visual Studio 2013 Update 2, você não precisa usar um arquivo de configurações de publicação. O Visual Studio é capaz de conectar diretamente ao Azure usando as credenciais que você fornece.

1. No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito no projeto, clique em **Adicionar** e depois em **Serviço Conectado...**. 

	![Adicionar serviço conectado](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Na caixa de diálogo Gerenciador de serviços, clique em **Criar serviço...**, em seguida selecione **&lt;Importar...&gt;** por meio de **Assinatura**, na caixa de diálogo Criar Serviço Móvel.  

	![create a new mobile service from VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Em Importar Assinaturas do Azure, clique em **Baixar arquivo de assinatura**, efetue logon em sua conta do Azure (se necessário), clique em **Salvar** quando o navegador solicitar para salvar o arquivo.

	![download subscription file in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	> [AZURE.NOTE] A janela de logon é exibida no navegador, que pode estar por trás da janela do Visual Studio. Lembre-se de anotar onde salvou o arquivo. publishsettings baixado. Você poderá ignorar esta etapa se o seu projeto já estiver conectado à sua assinatura do Azure.

4. Clique em **Procurar**, navegue para o local em que salvou o arquivo .publishetings, selecione o arquivo, clique em **Abrir** e então em **Importar**. 

	![import subscription in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	O Visual Studio importa os dados necessários para se conectar à sua assinatura do Azure. Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço. 

	> [AZURE.NOTE] Depois de importar as configurações de publicação, considere a possibilidade de excluir o arquivo .publishSettings baixado, pois ele contém informações que podem ser usadas por outras pessoas para acessar sua conta. Proteja o arquivo se você pretende mantê-lo para uso em outros projetos de aplicativo conectado.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/develop/mobile/how-to-guides/register-for-single-sign-on/
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
