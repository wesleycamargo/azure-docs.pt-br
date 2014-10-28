<properties linkid="dev-net-service-bus-amqp-overview" urlDisplayName="Azure Notification Hubs" pageTitle="Azure Notification Hubs" metaKeywords="Azure push notifications, Azure notification hubs, Azure messaging" description="Learn how to use push notifications in Azure. Code samples written in C# using the .NET API." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Azure Notification Hubs" authors="sethm" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/24/2014" ms.author="sethm"></tags>

# Hubs de Notificação do Azure

O suporte à notificação por push no Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.

## <span class="short-header">O que são notificações por push?</span>O que são notificações por push?

Smartphones e tablets têm a capacidade de "notificar" os usuários quando ocorre um evento. Em aplicativos da Windows Store, a notificação pode resultar em um *toast*: uma janela sem modo aparece, com um som, para sinalizar um novo envio por push. Em dispositivos Apple iOS, de maneira semelhante, o envio por push interrompe com uma caixa de diálogo solicitando que o usuário exiba ou feche a notificação. Um clique em **Exibir** abre o aplicativo que está recebendo a mensagem.

As notificações por push ajudam os dispositivos móveis a exibirem informações atualizadas enquanto continuam eficientes em termos de uso de energia. As notificações por push são um componente essencial para aplicativos de consumidor, onde são usadas para aumentar o envolvimento e o uso do aplicativo. As notificações também são úteis para empresas, quando informações atualizadas aumentam a capacidade de resposta dos funcionários a eventos de negócios.

Alguns exemplos específicos de cenários envolvimento móvel são:

1.  Atualização de um bloco no Windows 8 ou no Windows Phone com as informações financeiras atuais.
2.  Alertar um usuário com uma notificação do sistema de que algum item de trabalho foi atribuído a esse usuário, em um aplicativo empresarial baseado em fluxo de trabalho.
3.  Exibir uma notificação com o número de clientes potenciais de vendas atuais em um aplicativo CRM (por exemplo, o Microsoft Dynamics CRM).

## <span class="short-header">Como as notificações por push funcionam</span>Como as notificações por push funcionam

As notificações por push são fornecidas por meio de infraestruturas específicas à plataforma chamadas *Platform Notification Systems* (PNS). Um PNS oferece funções básicas (isto é, não há suporte para transmissão, personalização) e não tem nenhuma interface comum. Por exemplo, para enviar uma notificação a um aplicativo da Windows Store, um desenvolvedor deve entrar em contato com o WNS (Serviço de Notificação do Windows), para enviar uma notificação a um dispositivo iOS, o mesmo desenvolvedor precisa entrar em contato com o APNS (Serviços de Notificação por Push da Apple) e enviar a mensagem uma segunda vez.

No entanto, em um alto nível, todos os sistemas de notificação de plataforma seguem o mesmo padrão:

1.  O aplicativo cliente entra em contato com o PNS para recuperar seu *identificador*. O tipo de identificador depende do sistema. No WNS, o identificador é um URI ou "canal de notificação". No APNS, é um token.
2.  O aplicativo cliente armazena esse identificador no *back-end* do aplicativo para uso posterior. No WNS, o back-end normalmente é um serviço de nuvem. Na Apple, o sistema é chamado um *provedor*.
3.  Para enviar uma notificação por push, o back-end do aplicativo entra em contato com o PNS usando o identificador para direcionar para uma instância de aplicativo cliente específico.
4.  O PNS encaminha a notificação para o dispositivo especificado pelo identificador.

![][]

## <span class="short-header">Desafios das notificações por push</span>Desafios das notificações por push

Embora esses sistemas sejam muito poderosos, ainda sobra muito trabalho para o desenvolvedor do aplicativo para implementar até cenários comuns de notificação por push, como a transmissão ou o envio de notificações por push a um usuário.

As notificações por push são um dos recursos mais solicitados em serviços de nuvem para aplicativos móveis. A razão para isso é que a infraestrutura necessária para fazê-los funcionar é relativamente complexa e, na maioria das vezes, não relacionados à lógica comercial principal do aplicativo. Estes são alguns dos desafios na criação de uma infraestrutura de envio por push sob demanda:

-   **Dependência de plataforma.** Para enviar notificações para dispositivos em diferentes plataformas, várias interfaces devem ser codificadas no back-end. Não só os detalhes de baixo nível são diferentes, mas a apresentação da notificação (bloco, toast ou badge) também é dependente da plataforma. Essas diferenças podem levar a um código de back-end complexo e difícil de manter.

-   **Aumente.** Essa infraestrutura de dimensionamento tem dois aspectos:

1.  De acordo com as diretrizes de PNS, os tokens de dispositivo devem ser atualizados sempre que o aplicativo for iniciado. Isso leva a uma grande quantidade de tráfego (e a consequentes acessos ao banco de dados) apenas para manter os tokens do dispositivo atualizados. Quando o número de dispositivos cresce (possivelmente para milhões), o custo da criação e a manutenção dessa infraestrutura é significativo.
2.  A maioria dos PNS não oferece suporte à transmissão para vários dispositivos. Além disso, uma transmissão para milhões de dispositivos resulta em milhões de chamadas para os PNSs. A capacidade de expandir essas solicitações é rara, pois geralmente os desenvolvedores de aplicativos desejam manter a latência total (por exemplo, o último dispositivo a receber a mensagem não deve receber a notificação 30 minutos depois que as notificações foram enviadas, uma vez que, em muitos casos, o objetivo da notificação por push seria anulado).

-   **Roteamento.** O PNSs fornecem uma maneira de enviar uma mensagem a um dispositivo. No entanto, na maioria dos aplicativos, as notificações são destinadas a usuários e/ou grupos de interesse (por exemplo, todos os funcionários atribuídos a uma determinada conta de cliente). Dessa forma, para rotear as notificações para os dispositivos corretos, o back-end do aplicativo deve manter um registro que associe os grupos de interesse aos tokens dos dispositivos. Essa sobrecarga aumenta o tempo total de colocação no mercado e os custos de manutenção de um aplicativo.

## <span class="short-header">Por que usar Hubs de Notificação?</span>Por que usar Hubs de Notificação?

Os Hubs de Notificação fornecem uma infraestrutura de notificação por push pronta para uso que oferece suporte ao seguinte:

-   **Várias plataformas.** Os Hubs de Notificação fornecem uma interface comum para enviar notificações a todas as plataformas com suporte. O back-end do aplicativo pode enviar notificações em formatos específicos à plataforma ou independentes da plataforma. Os Hubs de Notificação podem enviar notificações por push a aplicativos da Windows Store, iOS, Android e Windows Phone.
-   **Roteamento Pub/Sub.** Ao enviar seu identificador a um Hub de Notificação, cada dispositivo pode especificar uma ou mais *marcas*. Para obter mais informações sobre marcas, consulte a seção a seguir. Marcas não precisam ser pré-provisionadas ou descartadas. As marcas fornecem uma maneira simples de enviar notificações aos usuários ou grupos de interesse. Como as marcas podem conter qualquer identificador específico ao aplicativo (como IDs de usuários ou de grupos), seu uso libera o back-end do aplicativo do ônus de precisar armazenar e gerenciar identificadores de dispositivos.
-   **Aumente.** Os Hubs de Notificação são dimensionados para milhões de dispositivos sem a necessidade de nova arquitetura ou fragmentação.

Os Hubs de Notificação usam uma infraestrutura de notificação por push de multiplataforma completa e dimensionável e reduz consideravelmente o código específico ao envio por push que executa no back-end do aplicativo. Os Hubs de Notificação implementam toda a funcionalidade de uma infraestrutura de envio por push. Os dispositivos são responsáveis apenas por registrar seus identificadores de PNS, e o back-end é responsável por enviar mensagens independentes de plataforma para os usuários ou grupos de interesse.

![][1]

## Próximas etapas

Você pode encontrar mais informações sobre Hubs de Notificação nestes tópicos:

-   **[Como usam os clientes os Hubs de Notificação][Como usam os clientes os Hubs de Notificação]**

-   **[Tutoriais e guias de Hubs de notificação][Tutoriais e guias de Hubs de notificação]**

-   **Tutoriais de introdução de Hubs de Notificação** ([iOS][iOS], [Android][Android], [Windows Universal][Windows Universal], [Windows Phone][Windows Phone], [Kindle][Kindle], [Xamarin.iOS][Xamarin.iOS], [Xamarin.Android][Xamarin.Android])

As referências relevantes da API .NET gerenciada para notificações push estão localizadas aqui:

-   [Microsoft.WindowsAzure.Messaging.NotificationHub][Microsoft.WindowsAzure.Messaging.NotificationHub]
-   [Microsoft.ServiceBus.Notifications][Microsoft.ServiceBus.Notifications]

  []: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [Como usam os clientes os Hubs de Notificação]: http://azure.microsoft.com/pt-br/services/notification-hubs
  [Tutoriais e guias de Hubs de notificação]: http://azure.microsoft.com/pt-br/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/pt-br/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/pt-br/library/microsoft.servicebus.notifications.aspx
