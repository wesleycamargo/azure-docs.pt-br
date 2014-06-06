<properties pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos do Windows Phone" metaKeywords="" description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento de Windows Phone. " metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a><a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo do Windows Phone 8 usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo serviço móvel.</p>
<p>Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial. No vídeo, Nick Harris fornece uma introdução aos Serviços Móveis e explica como criar seu primeiro serviço móvel e conectar-se a ele em um aplicativo da Windows Store.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Executar o Vídeo</span></a> <span class="time">13:18</span></div>
</div>

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permite que você grave sua lógica de negócios do lado do servidor nos idiomas com suporte de .NET usando o Visual Studio, consulte [Versão de back-end do .NET] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa de uma conta do Azure com os Serviços Móveis do Azure habilitados.</p> <ul> <li>Se você não tiver uma conta, será possível criar uma conta de avaliação gratuita em questão de minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A30A4DDE2&amp;returnurl=http%3A%2F%2FFpt-br%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F" target="_blank">Avaliação gratuita do Azure</a>.</li></ul> </div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo para Windows Phone</h2>

Depois de criar seu serviço móvel, você poderá seguir as etapas de início rápido no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se ao seu serviço móvel. 

Nesta seção você criará um novo aplicativo do Windows Phone 8 que estará conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Windows Phone 8** em **Escolher plataforma** e expanda **Criar um novo aplicativo do Windows Phone 8**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo do Windows Phone conectado ao seu serviço móvel.

  	![][7]

3. Se ainda não o fez, baixe e instale o [Visual Studio 2012 Express para Windows Phone] em seu computador local.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixe e execute o aplicativo**, clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows Phone

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução no Visual Studio.

2. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

3. No aplicativo, digite um texto significativo, como _Complete the tutorial_ e clique em **Salvar**.

   	![][10]

   	Isso envia uma solicitação POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	>[WACOM.NOTE]Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo MainPage.xaml.cs.

4. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![][11]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

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
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png





[6]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-windows-phone-get-started/mobile-vs-project-wp8.png

[10]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Começar com as notificações de envio]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[SDK dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão de back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started

