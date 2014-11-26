<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introdução às notificações por push nos Serviços Móveis usando o Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a> 
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows Store.
Neste tutorial você adiciona notificações de push utilizando o Serviço de Notificação de push da Windows (WNS) ao projeto de inicialização rápida. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Este tutorial adiciona notifica&ccedil;&otilde;es por push a um aplicativo da Windows Store criado no Visual Studio 2012. O Visual Studio 2013 inclui novos recursos que facilitam a configura&ccedil;&atilde;o de notifica&ccedil;&otilde;es por push em seu aplicativo da Windows Store usando Servi&ccedil;os M&oacute;veis. Para obter a vers&atilde;o do Visual Studio 2013, consulte <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js">Introdu&ccedil;&atilde;o &agrave;s notifica&ccedil;&otilde;es por push</a>.</p>
</div>

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis][Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]
2.  [Criar a tabela de registros][Criar a tabela de registros]
3.  [Adicionar notificações por push ao aplicativo][Adicionar notificações por push ao aplicativo]
4.  [Atualizar scripts para enviar notificações por push][Atualizar scripts para enviar notificações por push]
5.  [Inserir dados para receber notificações][Inserir dados para receber notificações]

Este tutorial exige o seguinte:

-   Microsoft Visual Studio 2012 Express para Windows 8
-   Ativar a conta da Windows Store

Este tutorial baseia-se no tutorial [Introdução aos dados][Introdução aos dados]. Antes de iniciar este tutorial, você deve primeiro concluir [o tutorial][Introdução aos dados].:

## <a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para a Windows Store

Para poder enviar notificações por push para aplicativos da Windows Store dos Serviços Móveis, você deve enviar seu aplicativo para a Windows Store. Em seguida, você deve configurar seu serviço móvel para se integrar com o WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o WNS. Em seguida, você criará uma nova tabela para armazenar registros.

## <a name="create-table"></a>Criar uma nova tabela

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">Adicionar notificações por push</span>Adicionar notificações por push a seus aplicativos

1.  Abra o arquivo default.js e insira o seguinte fragmento de código na sobrecarga do método **app.OnActivated**, imediatamente após o método **args.setPromise**:

        // Get the channel for the application.
        var channel;
        var channelOperation = Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (newChannel) {
                channel = newChannel;
            });

    Esse código adquire e armazena um canal de notificação por push sempre que o aplicativo é iniciado.

2.  Insira o código a seguir após o código que cria a instância **MobileServiceClient**:

        // Insert the new channel URI into the Registrations table.
        var registrationsTable = client.getTable('Registrations');
        registrationsTable.insert({ handle: channel.uri });         

    Esse código insere o canal atual na tabela Registrations.

3.  Abra o arquivo Package.appxmanifest e verifique se, na guia **Interface do Usuário do aplicativo**, **Compatível com toast** está definido como **Sim**.

    ![][0]

    Isso garante que seu aplicativo possa gerar notificações de toast.

## <a name="update-scripts"></a><span class="short-header">Atualizar o script de inserção</span>Atualizar o script de inserção registrado no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Clique em **TodoItem**, clique em **Script** e selecione **Inserir**.

    ![][1]

2.  Substitua a função de inserção com o seguinte código e **Salvar**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) para todos os canais armazenados na tabela **Registros**.

## <a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo

1.  No Visual Studio, pressione F5 para executar o aplicativo.

2.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][2]

    Observe que, após a inserção, o aplicativo receberá uma notificação por push do WNS.

    ![][3]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstra recurso básico de notificação por push fornecido pelos Serviços Móveis. Se seu aplicativo requer recursos mais avançados, como o envio de notificações em várias plataformas, roteamento baseado em assinatura ou volumes muito grandes, convém usar os Hubs de Notificação do Azure com seu serviço móvel. Para obter mais informações, consulte um dos seguintes tópicos de Hubs de Notificação:

-   [Introdução aos Hubs de Notificação][Introdução aos Hubs de Notificação]
    Saiba como aproveitar os Hubs de Notificação no seu aplicativo da Windows Store.

-   [Enviar notificações para assinantes][Enviar notificações para assinantes]
    Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

-   [Enviar notificações para usuários][Enviar notificações para usuários]
    Saiba como enviar as notificações por push de um Serviço Móvel para usuários específicos em qualquer dispositivo.

-   [Enviar notificações entre plataformas aos usuários][Enviar notificações entre plataformas aos usuários]
    Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][Introdução aos dados]
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    Saiba mais sobre como registrar e usar scripts de servidor.

-   [Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis][Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]
    Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.

 
 


  [Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]: #register
  [Criar a tabela de registros]: #create-table
  [Adicionar notificações por push ao aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
  [Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
  [Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
  [Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/
