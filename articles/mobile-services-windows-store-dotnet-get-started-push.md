<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga"></tags>

# Introdução às notificações por push nos Serviços Móveis (envio herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="C# da Windows Store" class="current">C# da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como o Visual Studio 2013 permite que você use os Serviços Móveis do Azure para enviar notificações por push para o seu aplicativo da Windows Store. Neste tutorial, você adicionará notificações por push utilizando o serviço WNS (Notificação por Push do Windows) ao projeto de início rápido, diretamente do Visual Studio. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

> [WACOM.NOTE]Este tópico oferece suporte para os serviços móveis *existentes* que ainda não foram *atualizados* para usar a integração de Hubs de Notificação. Quando você cria um serviço móvel *novo*, esta funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push][Introdução às notificações por push].
>
> Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para oferecer suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. *Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível*. Assim que você tiver feito a atualização, consulte esta versão da [Introdução às notificações por push][Introdução às notificações por push].

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis][Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]
2.  [Atualizar o código de notificação por push gerado][Atualizar o código de notificação por push gerado]
3.  [Inserir dados para receber notificações][Inserir dados para receber notificações]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você.

## <a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Adicionar e configurar as notificações por push no aplicativo

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013][mobile-services-create-new-push-vs2013]]

1.  Expanda **serviços**, **serviços móveis**, o nome do seu serviço, abra o arquivo de código gerado e, em seguida, inspecione o método **UploadChannel** que obtém a ID de instalação e o canal para o dispositivo e insere esses dados na nova tabela de canais.

    Uma chamada para esse método também foi adicionada pelo assistente **OnLaunched** ao manipulador de eventos no arquivo de código App.xaml.cs. Isso garante a tentativa do registro do dispositivo sempre que o aplicativo for iniciado.

2.  No Gerenciador de Servidores, expanda **Azure**, **Serviços Móveis**, o nome do seu serviço e **canais** e abra o arquivo insert.js.

    Esse arquivo, que está armazenado no seu serviço móvel, contém o código JavaScript que é executado quando um cliente envia uma solicitação para registrar um dispositivo, inserindo dados na tabela de canais.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>A vers&atilde;o inicial desse arquivo cont&eacute;m o c&oacute;digo que verifica a exist&ecirc;ncia de um registro existente para o dispositivo. Ela tamb&eacute;m cont&eacute;m o c&oacute;digo que envia uma notifica&ccedil;&atilde;o por push quando um novo registro &eacute; adicionado &agrave; tabela de canais. O c&oacute;digo que envia uma notifica&ccedil;&atilde;o por push pode ser inclu&iacute;do em qualquer arquivo de script registrado. O local desse script depende de como a notifica&ccedil;&atilde;o &eacute; disparada. Os scripts podem ser registrados em rela&ccedil;&atilde;o a uma opera&ccedil;&atilde;o de inser&ccedil;&atilde;o, atualiza&ccedil;&atilde;o, exclus&atilde;o ou leitura em uma tabela; como um trabalho agendado; ou como uma API personalizada. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Trabalhar com scripts de servidor em Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

3.  Pressione a tecla F5 para executar o aplicativo e verificar se uma notificação é recebida imediatamente do serviço móvel.

    Essa notificação foi gerada inserindo uma linha na nova tabela de canais, que é o registro do dispositivo.

Enquanto o código gerado facilita a demonstração de uma notificação quando o aplicativo é executado, geralmente, isso não é um cenário significativo. Em seguida, você removerá o código de notificação da tabela de canais e substituirá esse código, com algumas alterações, na tabela TodoItem.

## <a name="update-scripts"></a><span class="short-header">Atualizar o código</span>Atualizar o código de notificação por push gerado

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2][mobile-services-create-new-push-vs2013-2]]

## <a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo

1.  No Visual Studio, pressione F5 para executar o aplicativo.

2.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][]

    Observe que, após a inserção, o aplicativo receberá uma notificação por push do WNS.

    ![][1]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação de um aplicativo da Windows Store para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Introdução aos Hubs de Notificação][Introdução aos Hubs de Notificação]<br/>
    Saiba como aproveitar os Hubs de Notificação no seu aplicativo da Windows Store.

-   [Enviar notificações para assinantes][Enviar notificações para assinantes]<br/>
    Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

-   [Enviar notificações para usuários][Enviar notificações para usuários]<br/>
    Saiba como enviar as notificações por push de um Serviço Móvel para usuários específicos em qualquer dispositivo.

-   [Enviar notificações entre plataformas aos usuários][Enviar notificações entre plataformas aos usuários]<br/>
    Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][Introdução aos dados]<br/>
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]<br/>
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]<br/>
    Saiba mais sobre como registrar e usar scripts de servidor.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]<br/>
    Saiba mais sobre como usar os Serviços Móveis com o .NET.



  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/ "Back-end do JavaScript"
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]: #register
  [Atualizar o código de notificação por push gerado]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
  [Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [Trabalhar com scripts de servidor em Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=287178
  [mobile-services-create-new-push-vs2013-2]: ../includes/mobile-services-create-new-push-vs2013-2.md
  []: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
  [Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
  [Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
  [Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library/
