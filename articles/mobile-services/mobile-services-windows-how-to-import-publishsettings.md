<properties 
	pageTitle="Importar seu arquivo de configurações de publicação no Visual Studio 2013 | Serviços Móveis" 
	description="Saiba como importar um arquivo de configurações de publicação de assinatura para seu aplicativo de serviços móveis do Azure no Visual Studio 2013." 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Importar seu arquivo de configurações de assinatura no Visual Studio 2013

Antes de criar o serviço móvel, você deve importar o arquivo de configurações de publicação de sua assinatura do Azure no Visual Studio. Isso permite que o Visual Studio se conecte ao Azure em seu nome.

>[AZURE.NOTE]A partir do Visual Studio 2013 Update 2, você não precisa usar um arquivo de configurações de publicação. O Visual Studio é capaz de conectar diretamente ao Azure usando as credenciais que você fornece.

1. No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito no projeto, clique em **Adicionar** e, em seguida, em **Serviço Conectado**. 

	![adicionar serviço conectado](./media/mobile-services-windows-how-to-import-publishsettings/mobile-add-connected-service.png)

2. No diálogo Gerenciador de Serviços, clique em **Criar serviço...**, selecione **&lt;Importar...&gt;** em **Assinatura** no diálogo Criar Serviço Móvel.

	![criar um novo serviço móvel do VS 2013](./media/mobile-services-windows-how-to-import-publishsettings/mobile-create-service-from-vs2013.png)

3. Em Importar Assinaturas do Azure, clique em **Baixar arquivo de assinatura**, faça logon em sua conta do Azure (se necessário) e clique em **Salvar** quando o navegador solicitar que você salve o arquivo.

	![baixe o arquivo de inscrição no VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]A janela de logon é exibida no navegador, que pode estar por trás da janela do Visual Studio. Lembre-se de anotar onde salvou o arquivo. publishsettings baixado. Você poderá ignorar esta etapa se o seu projeto já estiver conectado à sua assinatura do Azure.

4. Clique em **Procurar**, navegue para o local em que salvou o arquivo .publishetings, selecione o arquivo, clique em **Abrir** e então em **Importar**.

	![importar assinatura no VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription-2.png)

	O Visual Studio importa os dados necessários para conectar-se à sua assinatura do Azure. Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço.

	> [AZURE.NOTE]Depois de importar as configurações de publicação, considere a possibilidade de excluir o arquivo .publishSettings baixado, pois ele contém informações que podem ser usadas por outras pessoas para acessar sua conta. Proteja o arquivo se você pretende mantê-lo para uso em outros projetos de aplicativo conectado.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->