<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="C# da Windows Store">C# da Windows Store</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo Android. Neste tutorial você adiciona notificações por push usando o serviço de Mensagens de Nuvem do Google (GCM) para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial apresenta e explica estas etapas:

1.  [Habilitar o sistema de mensagens em nuvem do Google][]
2.  [Configurar o serviço móvel para enviar solicitações por push][]
3.  [Atualizar o servidor para enviar notificações por push][]
4.  [Adicionar notificações de push para seu aplicativo][]
5.  [Habilitar notificações por push para teste local][]
6.  [Testar o aplicativo no serviço móvel publicado][]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][] ou a [Introdução aos dados][] para conectar seu projeto ao serviço móvel. Como tal, esse tutorial também requer o Visual Studio 2013.

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

## <span id="register"></span></a>Habilitar o sistema de mensagens em nuvem do Google

[WACOM.INCLUDE [Habilitar GCM][]]

## <span id="configure"></span></a>Configurar Serviços Móveis para enviar solicitações de push

1.  Faça logon no [Portal de Gerenciamento do Azure][], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][]

2.  Clique na guia **Push** e insira o valor **API Key** obtido do GCM no procedimento anterior e clique em **Salvar**.

    ![][1]

    <div class="dev-callout"><b>Importante</b>
<p>Quando voc&ecirc; define suas credenciais do GCM para notifica&ccedil;&otilde;es por push aprimoradas na guia Enviar por Push no portal, elas s&atilde;o compartilhadas com os Hubs de Notifica&ccedil;&atilde;o para configurar o hub de notifica&ccedil;&atilde;o com seu aplicativo.</p>
</div>

Seu serviço móvel agora está configurado para trabalhar com o GCM e Hubs de Notificação.

## <a name="download-the-service"></a><span class="short-header">Baixar o serviço</span>Baixar o serviço em seu computador local

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Testar o serviço</span>Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <span id="update-server"></span></a>Atualizar o servidor para enviar notificações por push

1.  No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra o TodoItemController.cs. Na parte superior do arquivo, inclua as seguintes instruções `using`:

        using System;
        using System.Collections.Generic;

2.  Atualize a definição do método `PostTodoItem` com o seguinte código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Esse código irá enviar uma notificação por push (com o texto do item inserido) depois de inserir um item de tarefa pendente. Se ocorrer um erro, o código adicionará uma entrada no log de erros que pode ser visualizada na guia **Logs** do serviço móvel no Portal de Gerenciamento.

## <a name="publish-the-service"></a><span class="short-header">Publicar o serviço</span>Publicar o serviço móvel no Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="update-app"></a>Adicionar notificações de push para seu aplicativo

### Verificar Versão de SDK do Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

A próxima etapa é instalar os serviços do Google Play. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo.

Se você for testar com um dispositivo mais antigo, consulte [Configurar o SDK do Google Play Services] para determinar o mínimo que pode ser definido para esse valor e defina-o de maneira apropriada.

### Inclua o Google Play Services no projeto

[WACOM.INCLUDE [Incluir Serviços de Reprodução][]]

### Incluir código

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Testar o aplicativo no serviço móvel publicado

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

### Se você estiver usando o emulador para teste...

Assegure-se de usar um Dispositivo Virtual Android (AVD) que suporte APIs do Google.

1.  Em **Janela**, selecione **Gerenciador de Dispositivo Virtual Android**, selecione seu dispositivo, clique em **Editar** (ou **Novo** se não tiver dispositivos).

    ![][2]

2.  Selecione **Apis do Google** (ou **APIs do Google x86**) em **Destino**, clique em Ok.

    ![][3]

    Destina-se ao AVD para usar APIs do Google. Se você tiver várias versões do SDK do Android instaladas, assegure-se de que o Nível da API corresponda àquele configurado nas propriedades do projeto anteriormente.

### <span id="local-testing"></span></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

### Executando o teste

1.  No menu **Executar** no Eclipse, clique em **Executar** para iniciar o aplicativo.

2.  No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no botão **Incluir**.

    ![][4]

3.  Passe o dedo para baixo da parte superior da tela para abrir a Central de Notificação do dispositivo para ver a notificação.

Este tutorial foi concluído com êxito.

## <a name="next-steps"> </a>Próximas etapas

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][]
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][]
    Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][]
    Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Como usar a biblioteca de cliente Android para os Serviços Móveis][]
    Saiba mais sobre como usar os Serviços Móveis com o Android.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "C# da Windows Store"
  [JavaScript da Windows Store]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "JavaScript da Windows Store"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [Back-end do .NET]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Back-end do .NET"
  [Back-end do JavaScript]: /en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "Back-end do JavaScript"
  [Habilitar o sistema de mensagens em nuvem do Google]: #register
  [Configurar o serviço móvel para enviar solicitações por push]: #configure
  [Atualizar o servidor para enviar notificações por push]: #update-server
  [Adicionar notificações de push para seu aplicativo]: #update
  [Habilitar notificações por push para teste local]: #local-testing
  [Testar o aplicativo no serviço móvel publicado]: #test-app
  [Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Habilitar GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [Incluir Serviços de Reprodução]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
  [Como usar a biblioteca de cliente Android para os Serviços Móveis]: /en-us/documentation/articles/mobile-services-android-how-to-use-client-library
