<properties 
	pageTitle="Introdução aos Hubs de Evento" 
	description="Siga este tutorial para começar a usar Hubs de eventos do Azure enviando eventos com C e recebendo-os em um cluster do Apache Storm" 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="c" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Introdução aos Hubs de Evento

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## Introdução

Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, consulte[Visão geral de Hubs de Evento].

Neste tutorial, você aprenderá como receber mensagens em um Hub de Eventos usando um aplicativo de console em C e recuperá-los em paralelo usando Apache Storm.

Para concluir este tutorial, você precisará do seguinte:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos pressupor a pilha gcc em uma [Máquina Virtual Linux do Azure](virtual-machines-linux-tutorial.md) com Ubuntu 14.04. Serão fornecidas instruções para outros ambientes em links externos.

+ Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos pressupor o [Eclipse](https://www.eclipse.org/).

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>

## Criar um Hub de Evento

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de aplicativo**, em seguida, **Barramento de serviço**, em seguida, **Hub de eventos**e, em seguida, **Criação rápida**.

   	![][1]

3. Digite um nome para seu Hub de Eventos, selecione a região desejada e clique em **Criar novo Hub de Eventos**.

   	![][2]

4. Clique no namespace que você acabou de criar (geralmente ***event hub name*-ns**).

   	![][3]

5. Clique na guia **Hubs de Evento** parte superior da página e clique no Hub de Evento que acabou de criar.

   	![][4]

6. Clique na guia **Configurar** na parte superior da página, adicione uma regra chamada **SendRule** com direitos *Send*, adicione outra regra chamada **ReceiveRule** com direitos *Listen* e clique em **Salvar**.

   	![][5]

7. Na mesma página, tome nota das chaves geradas para **SendRule** e **ReceiveRule**.

   	![][6c]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	Execute a classe **LogTopology** do Eclipse, então espere para ela iniciar os receptores para todas as partições.

2.	Execute o programa **sender** e veja os eventos aparecerem na janela do receptor.

   	![][23]

> [AZURE.NOTE] Neste tutorial, apenas use o Storm no modo local para fins de desenvolvimento. Consulte a [Visão geral do Storm no HDInsight] e a documentação oficial do [Apache Storm] para obter mais informações de implantações e padrões do Storm.

## Próximas etapas

Os seguintes recursos estão disponíveis para o desenvolvimento de aplicativos integrando Hubs e Storm.

- [Analisar dados do sensor com o Storm e do HDInsight] é um tutorial de cenário integral usando Hubs de Evento, Storm e HBase para receber dados do sensor em um cluster do Hadoop.
- [Desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# em Storm e HDInsight] é um tutorial sobre como escrever tubulações do Storm usando C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[23]: ./media/service-bus-event-hubs-getstarted/receive-storm3.png

<!-- Links -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Host do processador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão Geral de Hubs de Evento]: http://msdn.microsoft.com/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Visão geral do Storm no HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/
[Analisar dados do sensor com o Storm e do HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# em Storm e HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/


<!--HONumber=52-->