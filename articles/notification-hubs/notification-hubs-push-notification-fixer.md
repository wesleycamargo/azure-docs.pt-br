---
title: Diagnosticar notificações removidas nos Hubs de notificação do Azure
description: Saiba como diagnosticar problemas comuns com notificações removidas nos Hubs de Notificações do Microsoft Azure.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: eebf9ef63a8622c4cc431322b786fdf30f6352fe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925831"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnosticar notificações removidas nos Hubs de notificação do Azure

Uma pergunta comum sobre Hubs de notificação do Azure é como solucionar problemas quando as notificações de um aplicativo não aparecem nos dispositivos cliente. Os clientes querem saber onde e por que as notificações foram canceladas e como corrigir o problema. Este artigo identifica o motivo pelo qual as notificações podem ser removidas ou não recebidas pelos dispositivos. Ele também explica como determinar a causa raiz.

É fundamental compreender primeiro como os Hubs de Notificação efetua push das notificações para um dispositivo.

![Arquitetura dos Hubs de Notificação][0]

Em um fluxo de envio de notificação típico, a mensagem é enviada do *back-end do aplicativo* para os Hubs de Notificação. Os Hubs de notificação processa todos os registros. Ele leva em conta as marcas configuradas e expressões de marca para determinar os destinos. Os destinos são os registros que devem receber a notificação por push. Esses registros podem ser qualquer uma das nossas plataformas com suporte: Android, Baidu (dispositivos Android na China), SO Fire (Amazon) iOS, Windows e Windows Phone.

Com os destinos estabelecidos, os Hubs de Notificação efetuam push nas notificações para o *serviço de notificação por push* para a plataforma do dispositivo. Alguns exemplos são o serviço APNs (Apple Push Notification Service) para Apple e FCM (Firebase Cloud Messaging) para o Google. Os Hubs de Notificação efetuam push de notificações divididas em diversos lotes de registros. Ele se autentica com o serviço de notificação por push respectivos, com base nas credenciais que você definir no portal do Azure, em **configurar Hub de notificação**. O serviço de notificação por push encaminha as notificações para os respectivos *dispositivos cliente*.

O segmento final da entrega da notificação está entre o dispositivo e serviço de notificação por push da plataforma. Entrega da notificação pode falhar em qualquer um dos quatro estágios do processo de notificação por push (cliente, back-end do aplicativo, os Hubs de notificação e serviço de notificação por push da plataforma). Para obter mais informações sobre a arquitetura dos Hubs de Notificação, consulte [Visão geral dos Hubs de Notificação].

Pode ocorrer uma falha para entregar notificações inicial durante a fase de teste/preparo. Notificações removidas neste estágio podem indicar um problema de configuração. Se ocorrer uma falha para entregar notificações em produção, algumas ou todas as notificações poderão ser removidas. Um aplicativo mais profundo ou problema no padrão de mensagens é indicado neste caso.

A próxima seção examina os cenários em que as notificações poderão ser removidas, desde comuns ao raros.

## <a name="notification-hubs-misconfiguration"></a>Configuração incorreta dos Hubs de Notificação ##

Para enviar notificações para o serviço de notificação por push respectivos, os Hubs de notificação deve se autenticar no contexto do seu aplicativo. Você deve criar uma conta de desenvolvedor com o serviço de notificação da plataforma de destino (Microsoft, Apple, Google, etc.). Em seguida, você deve registrar seu aplicativo com o sistema operacional em que você obtenha um token ou chave que você pode usar para trabalhar com o PNS de destino.

Você precisa adicionar as credenciais da plataforma para o Portal do Azure. Se nenhuma notificação está acessando o dispositivo, a primeira etapa é garantir que as credenciais corretas sejam configuradas nos Hubs de notificação. As credenciais devem corresponder ao aplicativo que é criado sob uma conta de desenvolvedor específica da plataforma.

Para ver instruções passo a passo para concluir esse processo, consulte [Introdução aos Hubs de Notificações do Microsoft Azure].

Aqui estão algumas configurações incorretas comuns para observar:

### <a name="notification-hub-name-location"></a>Local de nome de hub de notificação

Verifique se o nome do seu hub de notificação (sem erros de grafia) é o mesmo em todos esses locais:
   * Em que você registra do cliente
   * Para onde enviar notificações do back-end
   * Em que você configurou as credenciais do serviço de notificação por push

Verifique se você usar as cadeias de caracteres de configuração de assinatura de acesso compartilhado corretas no cliente e encerrar o aplicativo novamente. Em geral, você deve usar **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no aplicativo de back-end. Isso concede permissões para enviar notificações para os Hubs de notificação.

### <a name="apn-configuration"></a>Configuração de APN ###

Você deve manter dois hubs diferentes: um para produção e outro para teste. Você deve carregar o certificado que você pode usar em um ambiente de área restrita para um hub separado do certificado/hub que você usará na produção. Não tente carregar tipos diferentes de certificados para o mesmo hub. Isso causará falhas de notificação.

Se você inadvertidamente carregue tipos diferentes de certificados para o mesmo hub, você deve excluir o hub e começar do zero com um novo hub. Se por alguma razão você não pode excluir o hub, você deve pelo menos excluir todos os registros existentes do hub.

### <a name="fcm-configuration"></a>Configuração de FCM ###

1. Certifique-se de que o *chave do servidor* obtidos das correspondências Firebase a chave do servidor registrado no portal do Azure.

   ![Chave do servidor Firebase][3]

2. Verifique se você configurou a **ID do projeto** no cliente. Você pode obter o valor da **ID do projeto** do painel do Firebase.

   ![ID do projeto Firebase][1]

## <a name="application-issues"></a>Problemas de aplicativos ##

### <a name="tags-and-tag-expressions"></a>Marcas e expressões de marca ###

Se você usar marcas ou expressões de marca para segmentar seu público-alvo, é possível que, quando você enviar a notificação, nenhum destino for encontrado. Esse erro se baseia nas marcas especificadas ou expressões de marca na chamada de envio.

Examine os registros para garantir que as marcas de correspondam quando você enviar uma notificação. Em seguida, verifique se o recebimento da notificação de clientes que têm esses registros.

Por exemplo, suponha que todos os seus registros com Hubs de notificação usam a marca "Politics". Se você, em seguida, enviar uma notificação com a marca "Esportes", a notificação não será enviada para qualquer dispositivo. Um caso complexo pode envolver a expressões de marca em que você registrou usando "Tag A" *ou* "Marca B", mas você direcionada a "marca A & & Tag b". A seção de dicas de autodiagnóstico posteriormente neste artigo mostra como examinar os registros e suas marcas.

### <a name="template-issues"></a>Problemas de modelo ###

Se você usar modelos, siga as diretrizes descritas em [Modelos].

### <a name="invalid-registrations"></a>Registros inválidos ###

Se o hub de notificação foi configurado corretamente e marcas ou expressões de marca foram usadas corretamente, destinos válidos serão encontrados. As notificações devem ser enviadas para esses destinos. Os Hubs de Notificação, em seguida, disparam vários lotes de processamento em paralelo. Cada lote envia mensagens para um conjunto de registros.

> [!NOTE]
> Como os Hubs de notificação processa os lotes em paralelo, a ordem na qual as notificações são entregues não é garantida.

Os Hubs de notificação é otimizado para um modelo de entrega de mensagem de "no-máximo uma vez". Tentamos realizar uma eliminação de duplicação para que nenhuma notificação seja entregue mais de uma vez para um dispositivo. Os registros são verificados para garantir que somente uma mensagem é enviada por identificador de dispositivo antes de serem enviado ao serviço de notificação por push.

Cada lote é enviada para o serviço de notificação por push, que por sua vez aceita e valida os registros. Durante esse processo, é possível que o serviço de notificação por push detecte um erro com um ou mais registros em um lote. O serviço de notificação por push, em seguida, retorna um erro para os Hubs de notificação e o processo será interrompido. O serviço de notificação por push remove esse lote completamente. Isso é especialmente verdadeiro com o APNs, que usa um protocolo de fluxo TCP.

Nesse caso, o registro com falha é removido do banco de dados. Em seguida, tentamos entregar a notificação novamente para o restante dos dispositivos nesse lote.

Para obter mais informações de erro sobre a tentativa de entrega com falha em um registro, você pode usar as APIs de REST de Hubs de notificação [telemetria por mensagem: Obter a telemetria de mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [comentários dos PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Para obter o código de exemplo, consulte o [Exemplo de envio de REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemas do serviço de notificação por push

Depois que o serviço de notificação por push recebe a notificação, ele entrega a notificação ao dispositivo. Neste ponto, os Hubs de notificação não tem controle sobre a entrega da notificação para o dispositivo.

Porque os serviços de notificação de plataforma são robustos, as notificações tendem a acessar os dispositivos em alguns segundos. Se o serviço de notificação por push é a limitação, os Hubs de notificação se aplica a uma estratégia de retirada exponencial. Se o serviço de notificação por push permanece inacessível por 30 minutos, há uma política em vigor para expirar e descartar as mensagens permanentemente.

Se um serviço de notificação por push tenta entregar uma notificação, mas o dispositivo estiver offline, a notificação é armazenada pelo serviço de notificação por push. Ele é armazenado para apenas um período de tempo limitado. A notificação é entregue ao dispositivo quando ele estiver disponível.

Cada aplicativo armazena apenas uma notificação recente. Se várias notificações são enviadas quando um dispositivo estiver offline, cada nova notificação faz com que o último deles sejam descartadas. Manter somente a notificação mais recente é chamado *coalescência* no APNs e *recolhimento* no FCM. (FCM usa uma chave de recolhimento). Quando o dispositivo permanecer offline por um longo tempo, as notificações que foram armazenadas para o dispositivo serão descartadas. Para obter mais informações, consulte [visão geral de APNs] e [Sobre mensagens de FCM].

Com os Hubs de notificação, você pode passar uma chave de coalescência por meio de um cabeçalho HTTP usando a API SendNotification genérica. Por exemplo, para o SDK do .NET, você usaria `SendNotificationAsync`. A API SendNotification também usa cabeçalhos HTTP que são passados como é o serviço de notificação por push respectivos.

## <a name="self-diagnosis-tips"></a>Dicas de autodiagnóstico

Aqui estão os caminhos para diagnosticar a causa raiz de notificações removidas nos Hubs de notificação.

### <a name="verify-credentials"></a>Verifique as credenciais ###

#### <a name="push-notification-service-developer-portal"></a>Portal de desenvolvedor do serviço de notificação por push ####

Verifique as credenciais no respectivo portal do desenvolvedor do serviço de notificação por push (APNs, FCM, Serviço de Notificação do Windows e assim por diante). Para obter mais informações, confira [Tutorial: Enviar notificações para aplicativos da Plataforma Universal do Windows usando Hubs de Notificação do Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portal do Azure ####

Para revisar e corresponder às credenciais com as obtidas do portal do desenvolvedor de serviço de notificação por push, vá para o **políticas de acesso** guia no portal do Azure.

![Políticas de acesso do Portal do Azure][4]

### <a name="verify-registrations"></a>Verificar registros

#### <a name="visual-studio"></a>Visual Studio ####

No Visual Studio, você pode se conectar ao Azure através do Gerenciador de servidores para exibir e gerenciar vários serviços do Azure, incluindo Hubs de notificação. Este atalho é útil principalmente para o seu ambiente de desenvolvimento/teste.

![Gerenciador de Servidores do Visual Studio][9]

Você pode exibir e gerenciar todos os registros no hub. Os registros podem ser categorizados por plataforma, registro nativo ou modelo, marca, identificador de serviço de notificação por push, ID de registro e data de validade. Também é possível editar um registro nesta página. Ele é especialmente útil para a edição de marcas.

Clique com botão direito no seu hub de notificação **Gerenciador de servidores**e selecione **diagnosticar**. 

![Gerenciador de servidores do Visual Studio: Diagnosticar menu](./media/notification-hubs-diagnosing/diagnose-menu.png)

Consulte a seguinte página:

![Visual Studio: Diagnosticar a página](./media/notification-hubs-diagnosing/diagnose-page.png)

Alterne para o **registros de dispositivo** página:

![Visual Studio: Registros de dispositivo](./media/notification-hubs-diagnosing/VSRegistrations.png)

Você pode usar **envio de teste** página para enviar uma mensagem de notificação de teste:

![Visual Studio: Envio de Teste](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Use o Visual Studio para editar os registros somente durante o desenvolvimento e teste e com um número limitado de registros. Se você precisar editar seus registros em massa, considere usar a exportação e importar a funcionalidade de registro descrita [How To: Exportar e modificar registros em massa](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Muitos clientes utilizam [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) para exibir e gerenciar seus hubs de notificação. O Gerenciador de Barramento de Serviço é um projeto de software livre. 

### <a name="verify-message-notifications"></a>Verificar as notificações de mensagem

#### <a name="azure-portal"></a>Portal do Azure ####

Para enviar uma notificação de teste para seus clientes sem precisar de um serviço de back-end em funcionamento, em **SUPORTE + SOLUÇÃO DE PROBLEMAS**, selecione **Envio de Teste**.

![Funcionalidade de Envio de Teste no Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Você também pode enviar notificações de teste do Visual Studio.

![Funcionalidade de Envio de Teste no Visual Studio][10]

Para obter mais informações sobre como usar os Hubs de Notificação com o Gerenciador de Servidores do Visual Studio, consulte estes artigos:

* [Como exibir os registros de dispositivo para os hubs de notificação](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Aprofundamento: Visual Studio 2013 Update 2 RC e SDK 2.3 do Azure]
* [Anunciando o lançamento do Visual Studio 2013 Atualização 3 e SDK do Azure 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Depurar notificações com falha e examinar o resultado da notificação

#### <a name="enabletestsend-property"></a>Propriedade EnableTestSend ####

Quando você envia uma notificação por meio de Hubs de notificação, a notificação é enfileirada inicialmente. Os Hubs de Notificação determinam os destinos corretos e, em seguida, enviam a notificação para o serviço de notificação por push. Se você estiver usando a API REST ou qualquer um dos SDKs do cliente, o retorno de chamada de seu envio significa apenas que a mensagem está na fila com Hubs de notificação. Ele não fornece informação sobre o que aconteceu quando os Hubs de notificação eventualmente enviou a notificação para o serviço de notificação por push.

Se a notificação não chegue ao dispositivo cliente, um erro pode ter ocorrido quando os Hubs de notificação tentou entregá-lo para o serviço de notificação por push. Por exemplo, o tamanho do conteúdo poderá exceder o máximo permitido pelo serviço de notificação por push, ou as credenciais configuradas em Hubs de Notificação podem ser inválidas.

Para obter informações sobre erros do serviço de notificação por push, você pode usar a propriedade [EnableTestSend]. Essa propriedade é habilitada automaticamente quando você envia mensagens de teste do portal ou do cliente do Visual Studio. Você pode usar essa propriedade para ver informações detalhadas de depuração e também por meio de APIs. No momento, é possível usá-la no SDK do .NET. Ele será adicionado a todos os SDKs de cliente eventualmente.

Para usar a propriedade `EnableTestSend` com a chamada REST, acrescente um parâmetro de cadeia de consulta chamado *test* ao final da sua chamada de envio. Por exemplo: 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Exemplo de SDK do .NET ####

Veja este exemplo de como usar o SDK do .NET para enviar uma notificação de pop-up nativo (notificação do sistema):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da execução, `result.State` simplesmente declara `Enqueued`. Os resultados não fornecem nenhuma informação sobre o que aconteceu com a notificação por push.

Em seguida, você pode usar a propriedade booliana `EnableTestSend`. Use a propriedade `EnableTestSend` ao iniciar `NotificationHubClient` para obter um status detalhado sobre os erros do serviço de notificação por push que ocorrem quando a notificação é enviada. A chamada de envio leva tempo adicional para retornar porque ele precisa primeiro Hubs de notificação para entregar a notificação para o serviço de notificação por push.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Saída de exemplo ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Esta mensagem indica que as credenciais configuradas nos Hubs de notificação são inválidas ou que há um problema com os registros no hub. Excluir esse registro e deixar que o cliente recrie o registro antes de enviar a mensagem.

> [!NOTE]
> O uso da propriedade `EnableTestSend` é extremamente limitado. Use essa opção somente em um ambiente de desenvolvimento/teste e com um conjunto limitado de registros. Depurar as notificações são enviadas para somente 10 dispositivos. Também há um limite no processamento de envios de depuração, em 10 por minuto.

### <a name="review-telemetry"></a>Telemetria de revisão ###

#### <a name="azure-portal"></a>Portal do Azure ####

No portal, é possível obter uma visão geral rápida de todas as atividades no seu hub de notificação.

1. Na guia **Visão geral**, é possível ver uma exibição agregada de registros, notificações e erros por plataforma.

   ![Painel Visão geral dos Hubs de Notificação][5]

2. Na guia **Monitor**, você pode adicionar várias outras métricas específicas da plataforma para uma análise mais profunda. Você pode examinar especificamente erros que são retornados quando os Hubs de notificação tenta enviar a notificação para o serviço de notificação por push.

   ![Log de atividades do Portal do Azure][6]

3. Comece revisando as **Mensagens de entrada**, **Operações de registro** e **Notificações com êxito**. Em seguida, acesse a guia por plataforma para examinar os erros que são específicos para o serviço de notificação por push.

4. Se as configurações de autenticação para o hub de notificação estiverem incorretas, a mensagem **Erro de autenticação do PNS** será exibida. É uma boa indicação para verificar as credenciais de serviço de notificação por push.

#### <a name="programmatic-access"></a>Acesso Programático ####

Para obter mais informações sobre o acesso programático, consulte [acesso programático](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Vários recursos relacionados à telemetria, como exportar e importar os registros e acesso à telemetria por meio de APIs, estão disponíveis apenas na camada de serviço Standard. Se você tentar usar esses recursos de gratuita ou básica a camada de serviço, você obterá uma mensagem de exceção se você usar o SDK. Se você usar os recursos diretamente das APIs REST, você obterá um erro HTTP 403 (proibido).
>
> Para usar os recursos relacionados à telemetria, primeiro certifique-se no portal do Azure que você esteja usando a camada de serviço padrão.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Visão geral dos Hubs de Notificação]: notification-hubs-push-notification-overview.md
[Introdução aos Hubs de Notificações do Microsoft Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: https://msdn.microsoft.com/library/dn530748.aspx
[Visão geral de APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Sobre mensagens de FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Aprofundamento: Visual Studio 2013 Update 2 RC e SDK 2.3 do Azure]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Anunciando o lançamento do Visual Studio 2013 Atualização 3 e SDK do Azure 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
