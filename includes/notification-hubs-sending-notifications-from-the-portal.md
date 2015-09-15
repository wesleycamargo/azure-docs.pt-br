

Notificações por push normalmente são enviadas em um serviço de back-end como Serviços Móveis ou ASP.NET usando uma biblioteca compatível. Você também pode usar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não está disponível para o back-end.

Aqui está uma lista de alguns outros tutoriais que talvez você queira examinar para o envio de notificações:

- Serviços Móveis do Azure: para ver um exemplo de como enviar notificações de um back-end dos Serviços Móveis do Azure integrado com Hubs de Notificação, consulte [Introdução às notificações por push nos Serviços Móveis](../mobile-services-javascript-backend-ios-get-started-push.md).  
- ASP.NET: [Usar Hubs de notificação para enviar notificações por push aos usuários](notification-hubs-aspnet-backend-ios-notify-users.md).
- SDK Java do Hub de Notificação do Azure: consulte [Como usar os Hubs de Notificação do Java](../articles/notification-hubs/notification-hubs-java-backend-how-to.md) para enviar notificações do Java. Isso foi testado no Eclipse para desenvolvimento no Android.
- PHP: [como usar Hubs de Notificação do PHP](../articles/notification-hubs/notification-hubs-php-backend-how-to.md).


Nesta próxima seção do tutorial, você aprenderá a usar a [interface REST do Hub de Notificação](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) para enviar a mensagem de notificação diretamente para seu aplicativo. Todos os dispositivos registrados recebem a notificação enviada por qualquer dispositivo.

<!----HONumber=August15_HO6-->