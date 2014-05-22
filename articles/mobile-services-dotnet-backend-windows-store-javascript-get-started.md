<properties linkid="develop-mobile-tutorials-get-started" urlDisplayName="Introdução" pageTitle="Introdução aos Serviços Móveis para aplicativos da Windows Store | Mobile Dev Center" metaKeywords="" description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento em C#, VB ou JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />


# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a><!--<a href="/pt-br/documentation/articles/get-started-html" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a><a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap" class="current">PhoneGap</a>--></div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started/"  title="Back-end do JavaScript" >Back-end do JavaScript</a>
</div>

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo da Windows Store usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ em HTML e JavaScript que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão do JavaScript] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos da Windows Store. 

>[WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started%2F" target="_blank">Avaliação gratuita do Azure</a>.<br />Este tutorial requer o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Uma versão de avaliação gratuita está disponível.

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar um novo aplicativo da Windows Store

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção você criará um novo aplicativo da Windows Store que está conectado ao seu serviço móvel.

1. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.
   
2. Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

  	![][7]

3. Se ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no computador local ou na máquina virtual.

4. Em **Baixar e executar seu aplicativo e serviço localmente**, selecione um idioma para seu aplicativo da Windows Store e clique em **Baixar**. 

  	Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

5. Em **Publicar o serviço na nuvem**, clique aqui para baixar o perfil de publicação. Salve o arquivo baixado em seu computador local e anote onde ele foi salvo.

	<div class="dev-callout"><strong>Observação sobre segurança</strong> <p>Depois de importar o perfil de publicação, considere a possibilidade de excluir o arquivo baixado pois ele contém informações que podem ser usadas por outras pessoas para acessar seus serviços.</p></div>

## Testar o aplicativo no serviço móvel local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo default.js.

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>No projeto do aplicativo da Windows Store, abra o arquivo App.xaml.cs, localize o código que cria uma instância de <a href="http://msdn.microsoft.com/pt-br/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, comente o código que cria esse cliente usando <em>localhost</em> e remova o comentário do código que cria o cliente usando a URL de serviço móvel remoto, de maneira semelhante à seguinte:</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>Agora, o cliente acessará o serviço móvel publicado no Azure.</p></li>

<li><p>Pressione a tecla <strong>F5</strong> para recompilar o projeto e iniciar o aplicativo.</p></li>

<li><p>No aplicativo, digite texto significativo, como <em>Concluir o tutorial</em>, em <strong>Inserir um TodoItem</strong> e clique em <strong>Salvar</strong>.</p>

<p>Isso envia uma solicitação de POST ao novo serviço móvel hospedado no Azure.</p>
</li>
</ol>

<!--This should be in the list HTML, but there is a bug-->
![][10]

## Próximas etapas
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
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-store-get-started
[Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

