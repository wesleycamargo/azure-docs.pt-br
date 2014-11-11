<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

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
    <a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title="Back-end do .NET">Back-end do .NET</a> | 
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um servi&ccedil;o de back-end baseado na nuvem a um aplicativo da Windows Store usando os Servi&ccedil;os M&oacute;veis do Azure.</p>
<p>Se voc&ecirc; preferir assistir a um v&iacute;deo, o clipe &agrave; direita segue as mesmas etapas deste tutorial. No v&iacute;deo, Scott Guthrie fornece uma introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis e explica como criar seu primeiro servi&ccedil;o m&oacute;vel e conectar-se a ele em um aplicativo da Windows Store.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">10:08:00</span></div>

</div>

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permita escrever sua lógica de negócios do servidor nas linguagens do .NET compatíveis usando o Visual Studio, consulte a versão para back-end do .NET deste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos da Windows Store.

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

> Este tutorial exige o Visual Studio 2013. Para conectar um aplicativo da Windows Store usando o Visual Studio 2012, siga as etapas no tópico [Introdução aos dados em Serviços Móveis usando o Visual Studio 2012][Introdução aos dados em Serviços Móveis usando o Visual Studio 2012].

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo da Windows Store

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo da Windows Store que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

    ![][1]

    Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não o fez, baixe e instale [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows] em seu computador local ou máquina virtual.

4.  Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5.  Em **Baixe e execute seu aplicativo**, selecione um idioma para seu aplicativo e clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução no Visual Studio 2013 Express para Windows.

2.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

3.  No aplicativo, digite um texto com sentido, como *Concluir o tutorial*, em **Inserir um TodoItem** e clique em **Salvar**.

    ![][3]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na segunda coluna do aplicativo.

    > [WACOM.NOTE]Você pode revisar o código que acessa o serviço móvel para consultar e inserir dados, que está localizado em um arquivo MainPage.xaml.cs (projeto C#/XAML) ou em um arquivo default.js (projeto JavaScript/HTML).

4.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    ![][4]

    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

    ![][5]

## Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   **Introdução aos dados** ( [C#][C#] / [JavaScript][JavaScript] )
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   **Introdução à autenticação** ( [C#][6] / [JavaScript][7] )
    Saiba como autenticar usuários de seu aplicativo.

-   **Introdução às notificações por push** ( [C#][8] / [JavaScript][9] )
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Introdução aos dados em Serviços Móveis usando o Visual Studio 2012]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
  [6]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
  [8]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
