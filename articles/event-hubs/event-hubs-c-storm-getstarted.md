<properties
	pageTitle="Introdução aos Hubs de Eventos com o C e Apache Storm | Microsoft Azure"
	description="Siga este tutorial para começar a usar os Hubs de Eventos do Azure; enviar eventos em C; e receber eventos em um cluster do Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="sethm"/>

# Introdução aos Hubs de Eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introdução

Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para saber mais, confira [Visão geral de Hubs de Eventos].

Neste tutorial, você aprenderá como receber mensagens em um Hub de Eventos usando um aplicativo de console em C e recuperá-los em paralelo usando Apache Storm.

Para concluir este tutorial, você precisará do seguinte:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos pressupor a pilha gcc em uma [Máquina Virtual Linux do Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04. Serão fornecidas instruções para outros ambientes em links externos.

+ Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos considerar o [Eclipse](https://www.eclipse.org/).

+ Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	Execute a classe **LogTopology** do Eclipse, então espere para ela iniciar os receptores para todas as partições.

2.	Execute o programa **sender** e veja os eventos aparecerem na janela do receptor.

	![][23]

> [AZURE.NOTE] Neste tutorial, apenas use o Storm no modo local para fins de desenvolvimento. Consulte a [Visão geral do HDInsight Storm] e a documentação oficial do [Apache Storm] para obter mais informações de implantações e padrões do Storm.

## Próximas etapas

Os seguintes recursos estão disponíveis para o desenvolvimento de aplicativos integrando Hubs e Storm.

- [Analisar dados do sensor com o Storm e do HDInsight][] é um tutorial de cenário integral usando Hubs de Eventos, Storm e HBase para receber dados do sensor em um cluster do Hadoop.
- [Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm][] e HDInsight é um tutorial sobre como escrever tubulações do Storm usando C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[portal clássico do Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de Eventos]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Visão geral do HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md/
[Analisar dados do sensor com o Storm e do HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md

<!---HONumber=AcomDC_0817_2016-->