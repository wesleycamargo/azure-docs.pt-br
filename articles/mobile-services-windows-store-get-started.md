<properties pageTitle="Introdução aos Serviços Móveis para aplicativos da Windows Store | Central de desenvolvedores móveis" metaKeywords="" description="Siga este tutorial como introdução ao uso dos Serviços Móveis do Azure para desenvolvimento da Windows Store em C#, VB ou JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />


# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store" class="current">Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/pt-br/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title="Back-end do .NET">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo da Windows Store usando os Serviços Móveis do Azure.</p>
<p>Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial. No vídeo, Scott Guthrie fornece uma introdução aos Serviços Móveis e explica como criar seu primeiro serviço móvel e conectar-se a ele em um aplicativo da Windows Store.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Execute o vídeo</span></a> <span class="time">10:08</span></div>
</div>

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permita escrever sua lógica de negócios do servidor nas linguagens do .NET compatíveis usando o Visual Studio, consulte a versão para back-end do .NET deste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos da Windows Store. 

>[WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para ver detalhes, consulte [Avaliação gratuita do Azure](http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

>Este tutorial exige o Visual Studio 2013. Para conectar um aplicativo da Windows Store usando o Visual Studio 2012, siga as etapas no tópico [Introdução aos dados em Serviços Móveis usando o Visual Studio 2012](/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo da Windows Store

Depois de criar seu serviço móvel, você poderá seguir as etapas de um início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se a seu serviço móvel. 

Nesta seção você criará um novo aplicativo da Windows Store que está conectado ao seu serviço móvel.

1. No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

   
2. Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

  	![][7]

3. Se você ainda não tiver feito isso, baixe e instale o [Visual Studio 2013 Express para Windows] no computador local ou na máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixar e executar o aplicativo**, selecione um idioma para o aplicativo e clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos no computador e abra o arquivo de solução no Visual Studio 2013 Express para Windows.

2. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, em **Inserir um TodoItem** e clique em **Salvar**.

   	![][10]

   	Isso envia uma solicitação POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na segunda coluna do aplicativo.

	>[WACOM.NOTE]Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, localizado no arquivo MainPage.xaml.cs (projeto C#/XAML) ou no arquivo default.js (projeto JavaScript/HTML).

4. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItems**.

   	![][11]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][12]

## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* **Introdução aos dados** ( [C#][Introdução aos dados] / [JavaScript][Introdução aos dos JS] )
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* **Introdução à autenticação** ( [C#][Introdução à autenticação] / [JavaScript][Introdução à autenticação JS] )
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* **Introdução às notificações de push** ( [C#][Introdução às notificações de push] / [JavaScript][Introdução às notificações de push JS] )
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-windows-store-get-started/mobile-vs2013-project.png

[10]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações de push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[Introdução aos dos JS]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
[Introdução à autenticação JS]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações de push JS]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão de back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

