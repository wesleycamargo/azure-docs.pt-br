---
title: "Introdução aos Hubs de Eventos em C* | Microsoft Docs"
description: "Siga este tutorial para começar a usar os Hubs de Eventos do Azure; enviar eventos em C; e receber eventos em Java usando o EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: c9aa7cebab12eafe71476ee45ef578d3201e69e5
ms.openlocfilehash: e7c38aa5a778d5e8a653e753e5794d7c7da31dc1


---
# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como usar o portal do Azure para criar um Hub de Eventos. Ele também mostra como coletar mensagens em um Hub de Eventos usando um aplicativo de console escrito em C#, e como recuperá-los em paralelo usando a biblioteca do Host do Processador de Eventos em Java.

Para concluir esse tutorial, você precisará do seguinte:

* [Microsoft Visual Studio](http://visualstudio.com)
* Uma conta ativa do Azure. <br/>Se não tiver uma, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Execute o projeto **Receptor** .
   
   ![][21]
2. Execute o projeto **Remetente** .
   
   ![][22]

## <a name="next-steps"></a>Próximas etapas
Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

* Um [aplicativo de exemplo completo que usa os Hubs de Eventos][sample application that uses Event Hubs].
* O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][Scale out Event Processing with Event Hubs].
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


