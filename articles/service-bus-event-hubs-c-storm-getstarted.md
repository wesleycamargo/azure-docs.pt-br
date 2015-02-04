<properties pageTitle="Introdução aos Hubs de eventos" metaKeywords="barramento de serviço do Azure, hub de eventos, introdução a hubs de eventos" description="Siga este tutorial para começar a usar Hubs de eventos do Azure enviando eventos com C e recebendo-os em um cluster do Apache Storm" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="c" ms.devlang="java" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Introdução aos Hubs de Eventos

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Eventos, você pode transformar e armazenar os dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte [Visão geral de Hubs de Eventos].

Neste tutorial, você aprenderá como receber mensagens em um Hub de Eventos usando um aplicativo de console em C e recuperá-los em paralelo usando Apache Storm.

Para concluir este tutorial, você precisará do seguinte:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos pressupor a pilha gcc em uma [Máquina Virtual Linux do Azure](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-tutorial/) com Ubuntu 14.04. Serão fornecidas instruções para outros ambientes em links externos.

+ Um ambiente de desenvolvimento Java configurado para executar [Maven](http://maven.apache.org/). Para este tutorial, vamos pressupor o [Eclipse](https://www.eclipse.org/).

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.

## Criar um Hub de Eventos

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Eventos** e, em seguida, **Criação Rápida**.

   	![][1]

3. Digite um nome para seu Hub de Eventos, selecione a região desejada e clique em **Criar novo Hub de Eventos**.

   	![][2]

4. Clique no namespace que você acabou de criar (geralmente ***event hub name*-ns**).

   	![][3]

5. Clique na guia **Hubs de Eventos** na parte superior da página e depois clique no Hub de Eventos recém-criado.

   	![][4]

6. Clique na guia **Configurar** na parte superior da página, adicione uma regra chamada **SendRule** com direitos de *Send*, adicione outra regra chamada **ReceiveRule** com direitos de *Listen* e clique em **Salvar**.

   	![][5]

7. Na mesma página, tome nota das chaves geradas para **SendRule** e **ReceiveRule**.

   	![][6c]

Seu Hub de Eventos agora está criado e você tem as cadeias de conexão necessárias para enviar e receber eventos.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	Execute a classe **LogTopology** do Eclipse, então espere para ela iniciar os receptores para todas as partições.

2.	Execute o programa **sender** e veja os eventos aparecerem na janela do receptor.

   	![][23]

> [AZURE.NOTE] Neste tutorial, apenas use o Storm no modo local para fins de desenvolvimento. Consulte a [Visão geral do HDInsight Storm] e a documentação oficial do [Apache Storm] para obter mais informações de implantações e padrões do Storm.

## Próximas etapas
Os seguintes recursos estão disponíveis para o desenvolvimento de aplicativos integrando Hubs e Storm.

- [Analisar dados do sensor com o Storm e do HDInsight] é um tutorial de cenário integral usando Hubs de Evento, Storm e HBase para receber dados do sensor em um cluster do Hadoop.
- [Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm e HDInsight] é um tutorial sobre como escrever tubulações do Storm usando C#.

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
[Visão geral de Hubs de Eventos]: http://msdn.microsoft.com/pt-br/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Visão geral do HDInsight Storm]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-overview/
[Analisar dados do sensor com o Storm e do HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Desenvolver aplicativos de processamento de dados de fluxo com SCP.NET e C# em Storm e HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
