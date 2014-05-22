<properties pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos iOS" metaKeywords="Aplicativo iOS do Azure, iOS para serviço móvel, introdução ao iOS no Azure" description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento no iOS. " metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="C# da Windows Store">C# da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS" class="current">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a>
	<!--<a href="/pt-br/documentation/articles/get-started-html" title="HTML">HTML</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>-->
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started/"  title="Back-end do JavaScript" >Back-end do JavaScript</a>
</div>

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores. 

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Baixar o serviço móvel para o computador local

Agora que você criou o serviço móvel, baixe o projeto de serviço móvel personalizado que pode ser executado no computador local ou na máquina virtual.

1. Clique no serviço móvel que você acabou de criar, em seguida, na guia quickstart, clique em **iOS** em **Escolher plataforma** e expanda **Criar um novo aplicativo iOS**.

	![][1]

2. Se ainda não tiver feito isso, baixe e instale o Visual Studio Professional 2013 ou uma versão posterior.

3. Clique em **Download** em **Baixar e publicar o serviço na nuvem**.

	Isso baixa o projeto do Visual Studio que implementa o serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

4. Além disso, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Criar um novo aplicativo iOS

Nesta seção você criará um novo aplicativo iOS que está conectado ao seu serviço móvel.

1. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **iOS** em **Escolher plataforma** e expanda **Criar um novo aplicativo iOS**.   

3. Se ainda não fez isso, baixe e instale o [Xcode] v4.4 ou uma versão posterior.	

4. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados do aplicativo.

5. Em **Baixe e execute seu aplicativo**, clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao serviço móvel, juntamente com o SDK do iOS dos Serviços Móveis. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Isso mostra como executar seu novo aplicativo cliente no serviço móvel em execução no Azure. Antes de testar o aplicativo iOS com o serviço móvel em execução em um computador local, você deve configurar o servidor Web e o firewall para permitir acesso a partir de seu computador de desenvolvimento do iOS. Para obter mais informações, consulte [Configurar o servidor web local para permitir conexões a um serviço móvel local](/ en-us/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

<!--

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-->

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versão de back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-ios-get-started 

