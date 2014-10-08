<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introdução aos dados nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# da Windows Store">C# da Windows Store</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-android/"  title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure como uma fonte de dados de back-end para um aplicativo Android. Neste tutorial, você criará um novo serviço móvel, baixará um projeto do Eclipse Android para um aplicativo que armazena dados na memória, integrará o serviço móvel ao aplicativo e exibirá as alterações de dados feitas durante a execução do aplicativo.

O serviço móvel que você criará neste tutorial oferecerá suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][] neste tópico.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial requer o Visual Studio 2013.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Criar um novo serviço móvel][]
2.  [Baixar o serviço localmente][]
3.  [Testar o serviço móvel][]
4.  [Publicar o serviço móvel no Azure][]
5.  [Baixar o projeto GetStartedWithData][]
6.  [Atualizar o aplicativo para usar o serviço móvel para acesso a dados][]
7.  [Testar o aplicativo no serviço móvel publicado][]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][]]

## <a name="download-the-service"></a><span class="short-header">Baixar o serviço</span>Baixar o serviço em seu computador local

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Testar o serviço</span>Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <a name="publish-the-service"></a><span class="short-header">Publicar o serviço</span>Publicar o serviço móvel no Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

### Obter o código de amostra

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][1]]

### Verificar Versão de SDK do Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

### Inspecionar e executar o código de amostra

[WACOM.INCLUDE [mobile-services-android-run-sample-code][]]

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][]]

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Testar o aplicativo no serviço móvel publicado

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1.  No menu **Executar**, clique em **Executar** para iniciar o projeto.

    Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

2.  Como antes, digite texto significativo e clique em **Adicionar**.

    Isso envia um novo item como uma inserção no serviço móvel.

    Você pode reiniciar o aplicativo para ver se as alterações foram persistidas no banco de dados no Azure. Também é possível examinar o banco de dados usando o Portal de Gerenciamento do Azure: as duas próximas etapas fazem isso para exibir as mudanças em seu banco de dados.

3.  No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![][]

4.  No Portal de Gerenciamento, execute um consulta para exibir as mudanças feitas pelo aplicativo da Windows Store. Sua consulta será semelhante à consulta a seguir, mas usará seu nome de banco de dados em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Isso conclui o tutorial **Introdução aos dados** para Android.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para trabalhar com dados nos Serviços Móveis.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

Tente um destes outros tutoriais:

-   [Introdução à autenticação][]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][]
    Saiba mais sobre como usar os Serviços Móveis com o .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "C# da Windows Store"
  [JavaScript da Windows Store]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "JavaScript da Windows Store"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [Back-end do .NET]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Back-end do .NET"
  [Back-end do JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-android/ "Back-end do JavaScript"
  [Versão de back-end do JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-android
  [Criar um novo serviço móvel]: #create-service
  [Baixar o serviço localmente]: #download-the-service-locally
  [Testar o serviço móvel]: #test-the-service
  [Publicar o serviço móvel no Azure]: #publish-mobile-service
  [Baixar o projeto GetStartedWithData]: #download-app
  [Atualizar o aplicativo para usar o serviço móvel para acesso a dados]: #update-app
  [Testar o aplicativo no serviço móvel publicado]: #test-app
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [1]: ../includes/download-android-sample-code.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  []: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-android
  [Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-android
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
