<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Importar seu arquivo de configurações de publicação de assinatura no Visual Studio 2013" pageTitle="Importar seu arquivo de configurações de publicação de assinatura no Visual Studio 2013 | Serviços Móveis" metaKeywords="importação de publishsettings no Azure, serviços móveis" description="Saiba como importar o arquivo de configurações de uma assinatura para seu aplicativo de Serviços Móveis do Azure Mobile no Visual Studio 2013." title="Importar seu arquivo de configurações de assinatura no Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

# Importar seu arquivo de configurações de assinatura no Visual Studio 2013

Antes de criar o serviço móvel, você deve importar o arquivo de configurações de publicação de sua assinatura do Azure no Visual Studio. Isso permite que o Visual Studio se conecte ao Azure em seu nome.  


1. No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e, em seguida, em **Serviço Conectado...**. 

	![adicionar serviço conectado](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Na caixa de diálogo Gerenciador de Serviços, clique em **Criar serviço...**, selecione **&lt;Importar...&gt;** da **Assinatura na caixa de diálogo Criar Serviço Móvel**.  

	![criar um novo serviço móvel do VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Em Importar Assinaturas do Azure, clique em **Baixar arquivo de assinatura**, efetue logon em sua conta do Azure (se necessário), clique em **Salvar** quando o navegador solicitar para salvar o arquivo.

	![baixe o arquivo de inscrição no VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>Nota</strong> <p>a janela de login é exibida no navegador, o que pode estar por trás da janela do Visual Studio. Lembre-se de anotar onde salvou o arquivo. publishsettings baixado. Você pode ignorar esta etapa se o projeto já estiver conectado à sua assinatura do Azure.</p></div> 

4. Clique em **Procurar**, navegue para o local em que salvou o arquivo .publishetings, selecione o arquivo, clique em **Abrir** e, em seguida, em **Importar**. 

	![importar assinatura no VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	O Visual Studio importa os dados necessários para conectar-se à sua assinatura do Azure. Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço. 

	<div class="dev-callout"><strong>Nota de segurança</strong> <p>depois de importar as configurações de publicação, considere a possibilidade de excluir o arquivo. publishsettings baixado devido ao fato de ele conter informações que podem ser usadas por outras pessoas para acessar sua conta. Proteja o arquivo se você pretende mantê-la para uso em outros projetos de aplicativo conectado.</p></div>

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

