---
title: "Introdução aos Hubs de Eventos em Java com o Apache Storm | Microsoft Docs"
description: "Siga este tutorial para começar a usar Hubs de Eventos do Azure enviando eventos com Java e recebendo-os em um cluster do Apache Storm."
services: event-hubs
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eacafbcddb81085c706d8c4b64c426b21ce06b79


---
# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para saber mais, veja a [Visão geral de hubs de eventos][Visão geral de hubs de eventos].

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
> Neste tutorial, apenas use o Storm no modo local para fins de desenvolvimento. Confira a [Visão geral do HDInsight Storm][Visão geral do HDInsight Storm] e a documentação oficial do [Apache Storm][Apache Storm] para obter mais informações de implantações e padrões do Storm.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Os seguintes recursos estão disponíveis para o desenvolvimento de aplicativos integrando Hubs e Storm.

* [Analisar dados do sensor com o Storm e do HDInsight] é um tutorial de cenário integral usando Hubs de Eventos, Storm e HBase para receber dados do sensor em um cluster do Hadoop.
* [Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm e HDInsight][Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm] é um tutorial sobre como escrever tubulações do Storm usando C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Host do Processador de Eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de hubs de eventos]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Visão geral do HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[Analisar dados do sensor com o Storm e do HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Nov16_HO3-->


