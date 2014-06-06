<properties pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos iOS" metaKeywords="Aplicativo de iOS do Azure, serviços móveis iOS, introdução ao Azure iOS" description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento do iOS. " metaCanonical="" services="" documentationCenter="Mobile" title="Começar com os Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# <a name="getting-started"> </a>Começar com os Serviços Móveis
<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started" title="iOS" class="current">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/pt-br/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title="Back-end .NET">Back-end .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started/"  title="Back-end JavaScript" class="current">Back-end JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo do iOS usando os Serviços Móveis do Azure.</p>
<p>Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Executar Vídeo</span></a> <span class="time">9:38</span></div>
</div>

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples de _To do list_ que armazena dados de aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permite que você grave sua lógica de negócios do lado do servidor nos idiomas com suporte de .NET usando o Visual Studio, consulte [Versão de back-end do .NET] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores. 

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo iOS</h2>

Depois de criar seu serviço móvel, você pode seguir as etapas de um início rápido no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se ao serviço móvel. 

Nesta seção você criará um novo aplicativo iOS que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **iOS** em **Escolher a plataforma** e expanda **Criar um novo aplicativo iOS**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo iOS conectado ao seu serviço móvel.

  	![][7]

3. Se ainda não fez isso, baixe e instale o [Xcode] v 4.4 ou uma versão posterior.

4. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.

5. Em **Baixe e execute o aplicativo**, clique em **Baixar**. 

  	Será baixado o projeto para o aplicativo _To do list_ que está conectado ao serviço móvel, juntamente com os Serviços Móveis iOS SDK. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>No Portal de Gerenciamento, clique na guia <strong>Dados</strong> e depois clique na tabela <strong>TodoItems</strong>.<p>

<p><img src="./media/mobile-services-ios-get-started/mobile-data-tab.png" alt=""></p>

   	<p>Isso permite que você procure os dados inseridos pelo aplicativo na tabela.</p>

   	<p><img src="./media/mobile-services-ios-get-started/mobile-data-browse.png" alt=""></p></li>
</ol>


## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Começar com os dados]
  <br/>Saiba mais sobre armazenamento e consulta de dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar os usuários de seu aplicativo com um provedor de identidade.

* [Começar com as notificações de envio] 
  <br/>Aprender a enviar uma notificação de envio muito básica para seu aplicativo.


<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Começar com as notificações de envio]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios

[Serviços Móveis iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versão de back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started

