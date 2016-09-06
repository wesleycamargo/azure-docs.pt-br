<properties
	pageTitle="Hubs de Notificação do Azure"
	description="Saiba como usar notificações por push no Azure. Amostras de código escrito em C# usando a API .NET."
	authors="wesmc7777"
	manager="erikre"
	editor=""
	services="notification-hubs"
	documentationCenter=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="hero-article"
	ms.date="08/25/2016"
	ms.author="wesmc"/>


#Hubs de Notificação do Azure

##Visão geral

Os Hubs de Notificação do Azure fornecem uma infraestrutura de notificação por push fácil de usar, multiplataforma e dimensionável que permite enviar notificações por push de qualquer back-end (na nuvem ou local) para qualquer plataforma móvel.

Com os Hubs de Notificação, você pode enviar facilmente notificações por push personalizadas entre plataformas, sem se preocupar com detalhes dos sistemas de notificação de plataforma diferentes (PNS). Com uma única chamada de API, você pode direcionar usuários individuais ou segmentos de público inteiros com milhões de usuários em todos os dispositivos.

Você pode usar os Hubs de Notificação para cenários empresariais e de consumidor. Por exemplo:

- Envie notificações de últimas notícias para milhões de pessoas com baixa latência (Os Hubs de Notificação potencializam os aplicativos Bing pré-instalados em todos os dispositivos do Windows e do Windows Phone.)
- Envie cupons baseados na localização para segmentos de usuários.
- Envie notificações de eventos para usuários ou grupos para aplicativos de esportes/finanças/jogos.
- Notifique os usuários sobre eventos corporativos, como novas mensagens/emails e vendas potenciais.
- Envie senhas de uso único exigidas para Multi-Factor Authentication.



##O que são notificações por push?

Os smartphones e tablets podem "notificar" os usuários quando ocorre um evento. Essas notificações podem assumir várias formas.

Em aplicativos da Windows Store e do Windows Phone, a notificação pode ter a forma de uma _notificação do sistema_: uma janela não restrita é exibida com um som para sinalizar uma nova notificação. Outros tipos de notificação com suporte são as notificações _lado a lado_, _bruta_ e _selo_. Para saber mais sobre os tipos de notificações com suporte nos dispositivos Windows, confira [Lado a lado, selos e notificações](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Em dispositivos Apple iOS, de maneira semelhante, o envio por push interrompe com uma caixa de diálogo solicitando que o usuário exiba ou feche a notificação. Um clique em **Exibir** abre o aplicativo que está recebendo a mensagem. Para saber mais sobre Notificações iOS, confira [Notificações iOS](http://go.microsoft.com/fwlink/?LinkId=615245).

As notificações por push ajudam os dispositivos móveis a exibirem informações atualizadas enquanto continuam eficientes em termos de uso de energia. Notificações podem ser enviadas por sistemas back-end para dispositivos móveis mesmo quando os aplicativos correspondentes em um dispositivo não estão ativos. As notificações por push são um componente essencial para aplicativos de consumidor, onde são usadas para aumentar o envolvimento e o uso do aplicativo. As notificações também são úteis para empresas, quando informações atualizadas aumentam a capacidade de resposta dos funcionários a eventos de negócios.

Alguns exemplos específicos de cenários envolvimento móvel são:

1.  Atualização de um bloco no Windows 8 ou no Windows Phone com as informações financeiras atuais.
2.  Alertar um usuário com uma notificação do sistema de que algum item de trabalho foi atribuído a esse usuário, em um aplicativo empresarial baseado em fluxo de trabalho.
3.  Exibir uma notificação com o número de clientes potenciais de vendas atuais em um aplicativo CRM (por exemplo, o Microsoft Dynamics CRM).

##Como as notificações por push funcionam

As notificações por push são fornecidas por meio de infraestruturas específicas à plataforma chamadas _Platform Notification Systems_ (PNS). Um PNS oferece funções básicas (isto é, não há suporte para transmissão, personalização) e não tem nenhuma interface comum. Por exemplo, para enviar uma notificação para um aplicativo da Windows Store, um desenvolvedor deve contatar o WNS (Serviço de Notificação do Windows). Para enviar uma notificação a um dispositivo iOS, o mesmo desenvolvedor precisa entrar em contato com o APNS (Apple Push Notification Service) e enviar a mensagem uma segunda vez. Os Hubs de Notificação do Azure ajudam fornecendo uma interface comum, além de outros recursos para dar suporte a notificações por push em cada plataforma.

No entanto, em um alto nível, todos os sistemas de notificação de plataforma seguem o mesmo padrão:

1.  O aplicativo cliente entra em contato com o PNS para recuperar seu _identificador_. O tipo de identificador depende do sistema. No WNS, o identificador é um URI ou "canal de notificação". No APNS, é um token.
2.  O aplicativo cliente armazena esse identificador no _back-end_ do aplicativo para uso posterior. No WNS, o back-end normalmente é um serviço de nuvem. Na Apple, o sistema é chamado um _provedor_.
3.  Para enviar uma notificação por push, o back-end do aplicativo entra em contato com o PNS usando o identificador para direcionar para uma instância de aplicativo cliente específico.
4.  O PNS encaminha a notificação para o dispositivo especificado pelo identificador.

![][0]

##Os desafios de notificações por push

Embora esses sistemas sejam muito poderosos, ainda sobra muito trabalho para o desenvolvedor do aplicativo para implementar até cenários comuns de notificação por push, como a transmissão ou o envio de notificações por push a usuários segmentados.

As notificações por push são um dos recursos mais solicitados em serviços de nuvem para aplicativos móveis. A razão para isso é que a infraestrutura necessária para fazê-los funcionar é relativamente complexa e, na maioria das vezes, não relacionados à lógica comercial principal do aplicativo. Estes são alguns dos desafios na criação de uma infraestrutura de envio por push sob demanda:

- **Dependência de plataforma.** Para enviar notificações para dispositivos em diferentes plataformas, várias interfaces devem ser codificadas no back-end. Não só os detalhes de baixo nível são diferentes, mas a apresentação da notificação (bloco, toast ou badge) também é dependente da plataforma. Essas diferenças podem levar a um código de back-end complexo e difícil de manter.

- **Escala.** Essa infraestrutura de dimensionamento tem dois aspectos:
	+ De acordo com as diretrizes de PNS, os tokens de dispositivo devem ser atualizados sempre que o aplicativo for iniciado. Isso leva a uma grande quantidade de tráfego (e a consequentes acessos ao banco de dados) apenas para manter os tokens do dispositivo atualizados. Quando o número de dispositivos cresce (possivelmente para milhões), o custo da criação e a manutenção dessa infraestrutura é significativo.

	+ A maioria dos PNS não oferece suporte à transmissão para vários dispositivos. Além disso, uma transmissão para milhões de dispositivos resulta em milhões de chamadas para os PNSs. A capacidade de expandir essas solicitações é rara, pois geralmente os desenvolvedores de aplicativos desejam manter baixa a latência total. Por exemplo, o último dispositivo a receber a mensagem não deve receber a notificação 30 minutos depois que as notificações foram enviadas, uma vez que, em muitos casos, o objetivo da notificação por push seria anulado.
- **Roteamento.** O PNSs fornecem uma maneira de enviar uma mensagem a um dispositivo. No entanto, na maioria dos aplicativos, as notificações são destinadas a usuários e/ou grupos de interesse (por exemplo, todos os funcionários atribuídos a uma determinada conta de cliente). Dessa forma, para rotear as notificações para os dispositivos corretos, o back-end do aplicativo deve manter um registro que associe os grupos de interesse aos tokens dos dispositivos. Essa sobrecarga aumenta o tempo total de colocação no mercado e os custos de manutenção de um aplicativo.

##Por que usar Hubs de Notificação?

Os Hubs de Notificação eliminam a complexidade: não é necessário gerenciar os desafios das notificações por push. Em vez disso, você pode usar um Hub de Notificação. Os Hubs de Notificação usam uma infraestrutura de notificação por push de multiplataforma completa e dimensionável e reduzem consideravelmente o código específico de envio por push que é executado no back-end do aplicativo. Os Hubs de Notificação implementam toda a funcionalidade de uma infraestrutura de envio por push. Os dispositivos são responsáveis somente por registrar os identificadores de PNS e o back-end é responsável por enviar mensagens independentes de plataforma para os usuários ou grupos de interesse, como mostra a figura abaixo:

![][1]


Os Hubs de Notificação fornecem uma infraestrutura de notificação por push pronta para uso com as seguintes vantagens:

- **Várias plataformas.**
	+  Suporte para todas as principais plataformas móveis. Os Hubs de Notificação podem enviar notificações por push a aplicativos da Windows Store, iOS, Android e Windows Phone.

	+  Os Hubs de Notificação fornecem uma interface comum para enviar notificações a todas as plataformas com suporte. Protocolos específicos de plataforma não requeridos. O back-end do aplicativo pode enviar notificações em formatos específicos à plataforma ou independentes da plataforma. O aplicativo se comunica somente com Hubs de Notificação.

	+  Gerenciamento de identificador de dispositivo. Os Hubs de Notificação mantêm o registro de identificadores e comentários dos PNS.

- **Funciona com qualquer back-end**: nuvem ou no local, .NET, PHP, Java, Nó, etc.

- **Escala.** Os Hubs de Notificação são dimensionados para milhões de dispositivos sem a necessidade de nova arquitetura ou fragmentação.


- **Conjunto avançado de padrões de entrega**:

	- *Transmissão*: permite a difusão quase simultânea para milhões de dispositivos com uma única chamada de API.

	- *Unicast/Multicast*: push para marcas que representam usuários individuais, incluindo todos os seus dispositivos, ou um grupo mais amplo; por exemplo, separar fatores de forma (tablet vs. telefone).

	- *Segmentação*: push para um segmento complexo definido por expressões de marca (por exemplo, os dispositivos em Nova York seguindo os Yankees).

	Ao enviar seu identificador a um Hub de Notificação, cada dispositivo pode especificar uma ou mais _marcas_. Para saber mais sobre [marcas]. Marcas não precisam ser pré-provisionadas ou descartadas. As marcas fornecem uma maneira simples de enviar notificações aos usuários ou grupos de interesse. Como as marcas podem conter qualquer identificador específico ao aplicativo (como IDs de usuários ou de grupos), seu uso libera o back-end do aplicativo do ônus de precisar armazenar e gerenciar identificadores de dispositivos.

- **Personalização**: cada dispositivo pode ter um ou mais modelos para obter personalização e localização por dispositivo sem afetar o código de back-end.

- **Segurança**: SAS (segredo de acesso compartilhado) ou autenticação federada.

- **Telemetria sofisticada**: disponível no portal e programaticamente.


##Integração com Aplicativos Móveis do Serviço de Aplicativo

Para facilitar uma experiência integrada e unificada nos serviços do Azure, os [Aplicativos Móveis do Serviço de Aplicativo] possui suporte interno para notificações de push usando Hubs de notificação. [Aplicativos Móveis do Serviço de Aplicativo] oferecem uma plataforma de desenvolvimento de aplicativos móveis altamente escalonável, disponível globalmente para os desenvolvedores corporativos e integradores de sistema e que traz um conjunto rico de recursos para desenvolvedores de aplicativos móveis.

Os desenvolvedores de aplicativos móveis podem utilizar Hubs de notificação com o fluxo de trabalho a seguir:

1. Recuperar o identificador de PNS do dispositivo
2. Registrar o dispositivo e [modelos] com Hubs de Notificação por meio da conveniente API de registro do SDK do cliente de Aplicativos Móveis
    + Observe que os aplicativos móveis removem imediatamente todas as marcas nos registros para fins de segurança. Trabalhar com Hubs de notificação do seu back-end diretamente para associar marcas a dispositivos.
3. Enviar notificações do seu back-end de aplicativo com Hubs de notificação

Aqui estão alguns recursos convenientes para desenvolvedores com essa integração:

- **SDKs de cliente de Aplicativos Móveis.** Estes SDKs de várias plataformas fornecem APIs simples para registro e se comunicam com o hub de notificação vinculado automaticamente com o aplicativo móvel. Os desenvolvedores não precisam se aprofundar nas credenciais de Hubs de Notificação e trabalhar com um serviço adicional.
    + Os SDKs marcam automaticamente o dispositivo fornecido com a ID de usuário autenticada de Aplicativos Móveis para habilitar o envio por push para o cenário do usuário.
    + Os SDKs usam automaticamente a ID de instalação de Aplicativos Móveis como GUID para se registrar com Hubs de Notificação, poupando os desenvolvedores da dificuldade de manter vários GUIDs de serviço.
    
- **Modelo de instalação.** Os Aplicativos Móveis funcionam com o modelo de push mais recente dos Hubs de Notificação para representar todas as propriedades de push associadas a um dispositivo em uma instalação de JSON que se alinha com Serviços de Notificação por Push e é fácil de usar.

- **Flexibilidade.** Os desenvolvedores sempre podem optar por trabalhar com Hubs de Notificação diretamente até mesmo com a integração em vigor.

- **Experiência em integrada no [portal do Azure].** O Push como um recurso é representado visualmente em aplicativos móveis e os desenvolvedores podem trabalhar facilmente com o hub de notificação associado por meio de aplicativos móveis.



##Próximas etapas

Você pode encontrar mais informações sobre Hubs de Notificação nestes tópicos:

+ **[Como os clientes usam os Hubs de Notificação]**

+ **[Tutoriais e guias de Hubs de notificação]**

+ **Tutoriais de introdução de Hubs de Notificação** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

As referências relevantes da API .NET gerenciada para notificações push estão localizadas aqui:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Como os clientes usam os Hubs de Notificação]: http://azure.microsoft.com/services/notification-hubs
  [Tutoriais e guias de Hubs de notificação]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Aplicativos Móveis do Serviço de Aplicativo]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
  [modelos]: notification-hubs-templates.md
  [portal do Azure]: https://portal.azure.com
  [marcas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_0831_2016-->