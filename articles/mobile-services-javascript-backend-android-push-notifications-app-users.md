<properties linkid="/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Enviar notificações por push para usuários autenticados

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="C# da Windows Store">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title="Back-end do .NET">Back-end do .NET</a> | 
<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

Este tópico mostra como enviar notificações por push a um usuário autenticado em um dispositivo registrado. Diferente do tutorial anterior de [notificação por push][notificação por push], este tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID de usuário atribuído. Por fim, o script do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Este tutorial explica o seguinte processo:

-   [Atualizar o serviço para solicitar autenticação para registro][Atualizar o serviço para solicitar autenticação para registro]
-   [Atualizar o aplicativo para fazer logon antes do registro][Atualizar o aplicativo para fazer logon antes do registro]
-   [Testar o aplicativo][Testar o aplicativo]

Este tutorial oferece suporte para aplicativos Android.

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

            // Define a payload for the Google Cloud Messaging toast notification.
            var payload = 
                '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

            // Get the ID of the logged-in user.
            var userId = user.userId;       

            request.execute({
                success: function() {
                    // If the insert succeeds, send a notification to all devices 
                    // registered to the logged-in user as a tag.
                    push.gcm.send(userId, payload, {
                        success: function(pushResponse) {
                            console.log("Sent push with " + userId + " tag:", pushResponse, payload);
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

    Esse script de inserção utiliza a marca de ID de usuário para enviar uma notificação por push (com o texto do item inserido) a todos os registros do sistema de Mensagens em Nuvem do Google criado pelo usuário conectado.

## <a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users][mobile-services-android-push-notifications-app-users]]

## <a name="test"></a>Testar o aplicativo

[WACOM.INCLUDE [mobile-services-android-test-push-users][mobile-services-android-test-push-users]]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->
<!-- Anchors. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users "Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/ "Back-end do JavaScript"
  [notificação por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Atualizar o serviço para solicitar autenticação para registro]: #register
  [Atualizar o aplicativo para fazer logon antes do registro]: #update-app
  [Testar o aplicativo]: #test
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-android-get-started-users/
  [mobile-services-javascript-backend-push-notifications-app-users]: ../includes/mobile-services-javascript-backend-push-notifications-app-users.md
  [mobile-services-android-push-notifications-app-users]: ../includes/mobile-services-android-push-notifications-app-users.md
  [mobile-services-android-test-push-users]: ../includes/mobile-services-android-test-push-users.md
