---
title: Enviar eventos com Hubs de Eventos do Azure em Java, receber em C# | Microsoft Docs
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
ms.date: 01/04/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 2e16874e20b233b93370ec81f8e534475c7c562a


---
# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, veja [Visão Geral dos Hubs de Eventos][Event Hubs overview].

Este tutorial mostra como receber mensagens em um Hub de Eventos usando um aplicativo de console em Java e como recuperá-las em paralelo usando a biblioteca do [Host do Processador de Eventos][Event Processor Host] em C#.

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

* Um [aplicativo de exemplo completo que usa os Hubs de Eventos][sample application that uses Event Hubs].
* O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][Scale out Event Processing with Event Hubs].

Para obter mais informações, consulte o [Centro de desenvolvedores do Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/java-send.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO4-->


