<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# <a name="getting-started-with-push"> </a>Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Back-end do .NET" >Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo Android. Neste tutorial você adiciona notificações por push usando o serviço de Mensagens de Nuvem do Google (GCM) para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

> [WACOM.NOTE]Este tutorial demonstra a integração dos Serviços Móveis com os Hubs de Notificação que é como você envia notificações por push do seu serviço móvel. Se você estiver usando um serviço móvel antigo que está usando o push herdado e ainda não foi atualizado para usar os Hubs de Notificação, *recomendamos que você atualize* como parte deste tutorial. Se você escolher não atualizar agora, você deve seguir esta versão do tutorial: [Introdução às notificações por push (herdado)][Introdução às notificações por push (herdado)].

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Habilitar o sistema de mensagens em nuvem do Google][Habilitar o sistema de mensagens em nuvem do Google]
2.  [Configurar os Serviços Móveis][Configurar os Serviços Móveis]
3.  [Adicionar notificações de push para seu aplicativo][Adicionar notificações de push para seu aplicativo]
4.  [Atualizar scripts para enviar notificações por push][Atualizar scripts para enviar notificações por push]
5.  [Inserir dados para receber notificações][Inserir dados para receber notificações]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução aos dados] para conectar seu projeto ao serviço móvel.

## <span id="register"></span></a>Habilitar o sistema de mensagens em nuvem do Google

> [WACOM.NOTE]Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para [accounts.google.com][accounts.google.com].

[WACOM.INCLUDE [Habilitar GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Em seguida, você usará esse valor de chave de API para ativar os Serviços Móveis para autenticar com o GCM e enviar notificações de push em nome do seu aplicativo.

## <span id="configure"></span></a>Configurar Serviços Móveis para enviar solicitações de push

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][0]

2.  Clique na guia **Push**, insira o valore **Chave do API** obtida do GCM no procedimento anterior e clique em **Salvar**.

    > [WACOM.NOTE]Quando você estiver concluindo este tutorial usando uma versão do serviço móvel antiga, você pode ver um link na parte inferior da guia **Push** que diz **Habilitar push aprimorado**. Clique nele agora para atualizar seu serviço móvel para se integrar com os Hubs de Notificação. Esta alteração não pode ser revertida. Para obter detalhes sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte [esta orientação][esta orientação].

    ![][1]

    <div class="dev-callout"><b>Importante</b>
<p>Quando voc&ecirc; define suas credenciais do GCM para notifica&ccedil;&otilde;es por push aprimoradas na guia Enviar por Push no portal, elas s&atilde;o compartilhadas com os Hubs de Notifica&ccedil;&atilde;o para configurar o hub de notifica&ccedil;&atilde;o com seu aplicativo.</p>
</div>

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com os GCM e os Hubs de Notificação.

## <span id="add-push"></span></a>Adicionar notificações de push para seu aplicativo

### Verificar Versão de SDK do Android

[WACOM.INCLUDE [Verificar SDK](../includes/mobile-services-verify-android-sdk-version.md)]

A próxima etapa é instalar os serviços do Google Play. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo.

Se estiver testando com um dispositivo mais antigo, consulte [Configurar o SDK do Google Play Services][Configurar o SDK do Google Play Services] para determinar o mínimo que pode ser definido para esse valor e defina-o de maneira apropriada.

### Inclua o Google Play Services no projeto

[WACOM.INCLUDE [Incluir Serviços de Reprodução](../includes/mobile-services-add-Google-play-services.md)]

### Incluir código

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

## <span id="update-scripts"></span></a>Atualizar o script de inserção registrados no Portal de Gerenciamento

1.  No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][2]

2.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][3]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3.  Substitua a função de inserção com o seguinte código e **Salvar**:

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    Isso registra um novo script de inserção, que usa o [objeto gcm][objeto gcm] para enviar uma notificação por push a todos os dispositivos registrados depois que a inserção for bem-sucedida.

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

### Configurando o emulador para teste

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

1.  Reinicie o Eclipse, em seguida, no Gerenciador de Pacotes, clique com botão direito do mouse no projeto, clique em **Propriedades**, em **Android**, marque **Google APIs** e clique em **OK**.

    ![][4]

    Isso tem como alvo o projeto para APIs do Google.

2.  Na **Janela**, selecione **Android Virtual Device Manager**, selecione o dispositivo e clique em **Editar**.

    ![][5]

3.  Selecione **Google APIs** em **Destino** e clique em OK.

    ![][6]

    Destina-se ao AVD para usar APIs do Google.

### Executando o teste

1.  No menu **Executar** no Eclipse, clique em **Executar** para iniciar o aplicativo.

2.  No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no botão **Incluir**.

    ![][7]

3.  Passe o dedo para baixo da parte superior da tela para abrir a Central de Notificação do dispositivo para ver a notificação.

Este tutorial foi concluído com êxito.

## <a name="next-steps"> </a>Próximas etapas

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]
    
	Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
    
	Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Como usar a biblioteca de cliente Android para os Serviços Móveis][Como usar a biblioteca de cliente Android para os Serviços Móveis]
    
	Saiba mais sobre como usar os Serviços Móveis com o Android.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    
	Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Habilitar o sistema de mensagens em nuvem do Google]: #register
  [Configurar os Serviços Móveis]: #configure
  [Adicionar notificações de push para seu aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-android-get-started/
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Habilitar GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [esta orientação]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Verificar SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [Configurar o SDK do Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
  [Incluir Serviços de Reprodução]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-android-get-started-users
  [Como usar a biblioteca de cliente Android para os Serviços Móveis]: /pt-br/documentation/articles/mobile-services-android-how-to-use-client-library
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
