---
title: "Introdução aos Hubs de Eventos no Java | Microsoft Docs"
description: "Siga este tutorial para começar a usar os Hubs de Eventos do Azure enviando eventos com Java e recebendo-os em C# usando o EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 1c9988ca-1473-4287-a033-f99122c429bc
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bcc75a6cdba08c7c94a556826e98ef24c199c37f


---
# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, consulte a [Visão geral dos Hubs de Eventos][Visão Geral dos Hubs de Eventos].

Este tutorial mostra como ingerir mensagens em um Hub de Eventos usando um aplicativo de console em Java e como recuperá-las em paralelo usando a biblioteca do [Host do Processador de Eventos][Host do Processador de Eventos] em C#.

Para concluir este tutorial, você precisará do seguinte:

* Um ambiente de desenvolvimento Java. Para este tutorial, vamos considerar o [Eclipse](https://www.eclipse.org/).
* [Microsoft Visual Studio](http://visualstudio.com)
* Uma conta ativa do Azure. <br/>Se você não tem uma conta, pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Execute o projeto **Receiver** do Visual Studio, então espere para ele iniciar os receptores para todas as partições.
   
   ![][21]
2. Execute o projeto **Remetente** .
   
   ![][22]

## <a name="next-steps"></a>Próximas etapas
Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

* Um [aplicativo de exemplo que usa os Hubs de Eventos completo][aplicativo de exemplo completo que usa os Hubs de Evento].
* O exemplo de [Expansão do processamento de eventos com o Hubs de Eventos][Escala horizontal do processamento de eventos com Hubs de Eventos].

Para obter mais informações, consulte o [Centro de desenvolvedores do Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/java-send.png

<!-- Links -->
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Host do Processador de Eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão Geral dos Hubs de Eventos]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


