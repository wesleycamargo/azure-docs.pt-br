<properties
    pageTitle="Introdução aos Hubs de Eventos em C | Microsoft Azure"
    description="Siga este tutorial para começar a usar os Hubs de Eventos do Azure, enviar eventos em C e receber eventos em Java usando o EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>


# <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução

Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte [Visão geral de Hubs de Eventos][].

Neste tutorial, você aprenderá a receber mensagens em um Hub de Eventos usando um aplicativo de console em C, e recuperá-los em paralelo usando a biblioteca do [Host do Processador de Eventos][] em C#.

Para concluir este tutorial, você precisará do seguinte:

+ Um ambiente de desenvolvimento C. Para este tutorial, vamos pressupor a pilha gcc em uma [Máquina Virtual Linux do Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) com Ubuntu 14.04. Serão fornecidas instruções para outros ambientes em links externos.

+ Microsoft Visual Studio Express para Windows

+ Uma conta ativa do Azure. <br/>Se você não tem uma conta, pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  Execute o projeto **Receptor** .

    ![][21]

2.  Execute o programa **Remetente** e veja os eventos que aparecerem na janela do receptor.

    ![][24]

## <a name="next-steps"></a>Próximas etapas

Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

- Um [aplicativo de exemplo completo que usa os Hubs de Evento][].
- O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][] .
- [Visão geral de Hubs de Evento][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Host do Processador de Eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão Geral dos Hubs de Eventos]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Oct16_HO2-->


