---
title: "Introdução aos Hubs de Eventos em Java com o Apache Storm | Microsoft Docs"
description: "Siga este tutorial para começar a usar Hubs de Eventos do Azure enviando eventos com Java e recebendo-os em um cluster do Apache Storm."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 52c9ded8af99c5ae0836be8e71394dd1e5072aff
ms.openlocfilehash: 7566a8d0643607ee80c056ed4410aefd930226b8


---
# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, veja [Visão Geral dos Hubs de Eventos][Event Hubs Overview].

Este tutorial descreve como coletar mensagens em um Hub de Eventos usando um aplicativo de console em Java e recuperá-los em paralelo usando o Apache Storm.

Para concluir este tutorial, você precisará do seguinte:

* Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos considerar o [Eclipse](https://www.eclipse.org/).
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Execute a classe **LogTopology** do Eclipse, então espere para ela iniciar os receptores para todas as partições.
2. Execute o projeto **Remetente**, pressione **Enter** na janela do console e veja os eventos aparecerem na janela do receptor.
   
    ![][22]

> [!NOTE]
> Neste tutorial, apenas use o Storm no modo local para fins de desenvolvimento. Confira a [Visão geral do HDInsight Storm][HDInsight Storm Overview] e a documentação oficial do [Apache Storm][Apache Storm] para obter mais informações sobre as implantações e padrões do Storm.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Os seguintes recursos estão disponíveis para o desenvolvimento de aplicativos integrando Hubs e Storm.

* [Analisar dados do sensor com o Storm e o HDInsight] é um tutorial de cenário completo que usa os Hubs de Eventos, Storm e HBase para ingerir dados do sensor em um cluster do Hadoop.
* [Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm e HDInsight][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight] é um tutorial sobre como criar pipelines do Storm usando C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm Overview]: ../hdinsight/hdinsight-storm-overview.md
[Analisar dados do sensor com o Storm e o HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO2-->


