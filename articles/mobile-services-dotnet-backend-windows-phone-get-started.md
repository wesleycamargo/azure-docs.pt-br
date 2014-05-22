<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Introdução (WP8)" pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos Windows Phone" metaKeywords="" description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento para Windows Phone. " metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a>
	<!--<a href="/pt-br/documentation/articles/get-started-android" title="Android">Android</a>
		<a href="/pt-br/documentation/articles/get-started-html" title="HTML">HTML</a>
		<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
		<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
		<a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
		<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>-->
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started/"  title="Back-end do JavaScript" >Back-end do JavaScript</a>
</div>

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo Windows Phone 8 usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

>[WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure com o recurso Serviços Móveis habilitado. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A30A4DDE2&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F" target="_blank">Avaliação Gratuita do Azure</a>. <br />Este tutorial requer o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Uma versão de avaliação gratuita está disponível.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar um novo aplicativo Windows Phone

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção você criará um novo aplicativo do Windows Phone 8 que estará conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Windows Phone 8** em **Escolher plataforma** e expanda **Criar um novo aplicativo Windows Phone 8**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo do Windows Phone conectado ao seu serviço móvel.

  	![][7]

3. Se ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no computador local ou na máquina virtual.

4. Em **Baixar e publicar o serviço na nuvem**, clique em **Baixar**. 

  	Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

5. Em **Publicar o serviço na nuvem**, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel no computador local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

>[WACOM.NOTE]Há etapas de configuração adicionais necessárias para executar um aplicativo Windows Phone que se conecta a um serviço local. Não mostraremos como fazer isso neste tópico, mas você pode obter mais informações em [Como conectar-se a um serviço web local no emulador do Windows Phone 8] 

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>No projeto do aplicativo Windows Phone, abra o arquivo App.xaml.cs, localize o código que cria uma instância de <a href="http://msdn.microsoft.com/pt-br/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, comente o código que cria esse cliente usando <em>localhost</em> e remova o comentário do código que cria o cliente usando a URL de serviço móvel remoto, de maneira semelhante à seguinte:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXXX-APPLICATION-KEY-XXXXXXXX");</code></pre>

	<p>Agora, o cliente acessará o serviço móvel publicado no Azure.</p></li>

<li><p>Pressione a tecla <strong>F5</strong> para recompilar o projeto e iniciar o aplicativo.</p></li>

<li><p>No aplicativo, digite um texto significativo, como <em>Concluir o tutorial</em> e clique em <strong>Salvar</strong>.</p>

<p>Isso envia uma solicitação de POST ao novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.</p>
	<div class="dev-callout"> 
	<b>Observação</b> 
   	<p>Você pode rever o código que acessa o serviço móvel para consultar e inserir dados, que está localizado no arquivo MainPage.xaml.cs.</p> 
 	</div>
</li>
</ol>

![][10]

Isso mostra como executar seu novo aplicativo cliente no serviço móvel em execução no Azure. Antes de testar o aplicativo Windows Phone com o serviço móvel em execução em um computador local, você deve configurar o servidor Web e o firewall para permitir acesso a partir de seu dispositivo ou emulador do Windows Phone. Para obter mais informações, consulte [Configurar o servidor web local para permitir conexões a um Serviço Móvel local](/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

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
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png

[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[SDK dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão de back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started
[Como conectar-se a um serviço web local no emulador do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkId=391930

