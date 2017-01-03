---
title: "Introdução aos Hubs de Eventos em C* | Microsoft Docs"
description: "Siga este tutorial para começar a usar os Hubs de Eventos do Azure com C# e a usar o Host do Processador de Eventos."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 015dbab81f807cd05d1fedb97f5fc62a012f2084
ms.openlocfilehash: 54aaf68864fc83501b860ecc3daef1c0216c0140


---
# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como usar o portal do Azure para criar um Hub de Eventos. Ele também mostra como coletar mensagens em um Hub de Eventos usando um aplicativo de console escrito em C# e como recuperá-los em paralelo usando a biblioteca do [Host do Processador de Eventos][Event Processor Host] em C#.

Para concluir esse tutorial, você precisará do seguinte:

* [Microsoft Visual Studio](http://visualstudio.com)
* Uma conta ativa do Azure. Se não tiver uma, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No Visual Studio, abra o projeto **Destinatário** que você criou anteriormente.
2. Clique com o botão direito do mouse na solução **Destinatário**, clique em **Adicionar** e em **Projeto Existente**.
3. Localize o arquivo Sender.csproj existente e, em seguida, clique duas vezes nele para adicioná-lo à solução.
4. Novamente, clique com o botão direito do mouse na solução **Destinatário** e clique em **Propriedades**. A página de propriedades **Destinatário** será exibida.
5. Clique em **Projeto de Inicialização** e clique no botão **Vários projetos de inicialização**. Defina a caixa **Ação** de ambos os projetos **Destinatário** e **Remetente** como **Iniciar**.
   
    ![][19]
6. Clique em **Dependências do Projeto**. Na caixa **Projetos**, clique em **Remetente**. Na caixa **Depende de**, verifique se o **Destinatário** está marcado.
   
    ![][20]
7. Clique em **OK** para descartar o diálogo **Propriedades**.
8. Pressione F5 para executar o projeto **Destinatário** no Visual Studio e aguarde que ele inicie os destinatários para todas as partições.
   
   ![][21]
9. O projeto **Remetente** será executado automaticamente. Pressione **Enter** na janela do console e veja os eventos aparecerem na janela do destinatário.
   
   ![][22]

Pressione **Ctrl+C** na janela **Remetente** para encerrar o aplicativo do Remetente e pressione **Enter** na janela do Destinatário para finalizar o aplicativo.

## <a name="next-steps"></a>Próximas etapas
Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

* Um [aplicativo de exemplo completo que usa os Hubs de Eventos][sample application that uses Event Hubs].
* [Host do Processador de Eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][Scale out Event Processing with Event Hubs].
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Dec16_HO2-->


