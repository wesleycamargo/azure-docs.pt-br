<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Enviar notificações por push para usuários autenticados

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone" >Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar as notificações por push a um usuário autenticado em um dispositivo iOS registrado. Diferente do tutorial anterior de [notificação por push][notificação por push], esse tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente iOS possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID de usuário atribuído. Por fim, o script do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Este tutorial explica o seguinte processo:

-   [Atualizar o serviço para solicitar autenticação para registro][Atualizar o serviço para solicitar autenticação para registro]
-   [Atualizar o aplicativo para fazer logon antes do registro][Atualizar o aplicativo para fazer logon antes do registro]
-   [Testar o aplicativo][Testar o aplicativo]

Esse tutorial suporta aplicativos do Windows Phone 8.0 e do Windows Phone 8.1 ("Silverlight"). Para aplicativos da Loja do Windows Phone 8.1, consulte a [versão da Windows Store deste tópico][versão da Windows Store deste tópico].

## Pré-requisitos

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Adiciona um requisito de logon ao aplicativo de amostra TodoList.

-   [Introdução às notificações por push][notificação por push]
    Configura o aplicativo de amostra TodoList para notificações por push usando Hubs de Notificação.

Depois de ter concluído ambos os tutoriais, você pode evitar que usuários não autenticados se registrem para notificações por push de seu serviço móvel.

## <a name="register"></a>Atualizar o serviço para solicitar autenticação para registro

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users][mobile-services-javascript-backend-push-notifications-app-users]]

1.  Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

        function insert(item, user, request) {

                function insert(item, user, request) {
                    request.execute();
                    setTimeout(function() {
                        push.apns.send(null, {
                            alert: "Alert: " + item.text,
                            payload: {
                                "Hey, a new item arrived: '" + item.text + "'"
                            }
                        });
                    }, 2500);
                }

        }

    Esse script de inserção utiliza a marca de ID de usuário para enviar uma notificação por push (com o texto do item inserido) a todos os registros do aplicativo do Windows Phone (MPNS) criado pelo usuário conectado.

## <a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login][mobile-services-ios-push-notifications-app-users-login]]

## <a name="test"></a>Testar o aplicativo

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app][mobile-services-ios-push-notifications-app-users-test-app]]

<!-- Anchors. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users "Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "Back-end do JavaScript"
  [notificação por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Atualizar o serviço para solicitar autenticação para registro]: #register
  [Atualizar o aplicativo para fazer logon antes do registro]: #update-app
  [Testar o aplicativo]: #test
  [versão da Windows Store deste tópico]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-ios-get-started-users/
  [mobile-services-javascript-backend-push-notifications-app-users]: ../includes/mobile-services-javascript-backend-push-notifications-app-users.md
  [mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
  [mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md
