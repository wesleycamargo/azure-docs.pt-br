<properties pageTitle="Introdução aos Serviços Móveis para aplicativos Xamarin iOS" metaKeywords="" description="Siga este tutorial para obter uma introdução aos Serviços Móveis do Azure para desenvolvimento em Xamarin iOS." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors="craigd" solutions="" writer="glenga" manager="" editor="" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/pt-br/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/" title="Back-end do .NET">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin.iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo serviço móvel.</p>
<p>Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o vídeo</span></a> <span class="time">10:05</span></div>
</div>

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial requer o XCode 4.5 e o iOS 5.0 ou versões posteriores bem como o [Xamarin Studio] para OS X ou o plug-in Xamarin Visual Studio para Visual Studio no Windows.

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2><span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo Xamarin.iOS</h2>

Depois de criar seu serviço móvel, você poderá seguir um início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para se conectar a seu serviço móvel. 

Nesta seção você criará um novo aplicativo Xamarin.iOS que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Xamarin.iOS** sob **Escolher Plataforma** e expanda **Criar um novo aplicativo Xamarin.iOS**.

	![][6]

	Isso exibe as três etapas fáceis de criar um aplicativo Xamarin.iOS conectado a seu serviço móvel.

  	![][7]

3. Se ainda não fez isso, baixe e instale o [Xcode] v 4.4 ou uma versão posterior e o [Xamarin Studio].

4. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixar e executar aplicativo**, clique em **Baixar**. 

	Isso baixa o projeto para o aplicativo _To do list_ que está conectado a seu serviço móvel e faz referência ao componente dos Serviços Móveis do Azure para Xamarin.iOS. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

<h2><span class="short-header">Executar o aplicativo</span>Executar seu novo aplicativo Xamarin.iOS</h2>

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução **XamarinTodoQuickStart.iOS.sln** usando o Xamarin Studio ou o Visual Studio.

	![][8]

	![][9]

2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique no ícone de adição (**+**).

	![][10]

	Isso envia uma solicitação POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	<div class="dev-callout"> 
	<b>Observação</b> 
   	<p>Você pode examinar o código que acessa seu serviço móvel para consultar e inserir dados que estão localizados no arquivo C# TodoService.cs.</p> 
 	</div>

4. De volta ao Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItems**.

	![][11]

	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

	![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.


<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[SDK do iOS de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Gerenciamento]: https://manage.windowsazure.com/

