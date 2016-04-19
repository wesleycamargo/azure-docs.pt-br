<properties
	pageTitle="Introdução aos Hubs de Eventos em C# | Microsoft Azure"
	description="Siga este tutorial para começar a usar os Hubs de Eventos do Azure com C# e a usar o EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/12/2016"
	ms.author="sethm"/>

# Introdução aos Hubs de Evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introdução

Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como usar o portal clássico do Azure para criar um Hub de Eventos. Ele também mostra como coletar mensagens em um Hub de Eventos usando um aplicativo de console escrito em C#, e como recuperá-los em paralelo usando a biblioteca do [Host do Processador de Eventos][] em C#.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2013 ou posterior, ou Microsoft Visual Studio Express para Windows. Os exemplos neste artigo usam o Visual Studio 2015.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma, poderá criar uma conta gratuita em poucos minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank").

## Criar um Hub de Evento

1. Faça logon no [portal clássico do Azure][] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, em seguida, **Barramento de Serviço**, em seguida, **Hub de eventos** e, em seguida, **Criação rápida**.

	![][1]

3. Digite um nome para o Hub de Evento, selecione a região desejada e clique em **Criar um novo Hub de Evento**.

	![][2]

4. Se você não selecionou explicitamente um namespace existente em uma determinada região, o portal criará um para você (geralmente, ***o nome do hub de eventos*-ns**). Clique no namespace (neste exemplo, **eventhub-ns**).

	![][3]

5. Clique na guia **Hubs de Eventos** no início da página e clique no Hub de Evento que acabou de criar.

	![][4]

6. Clique na guia **Configurar** na parte superior, adicione uma regra denominada **SendRule** com direitos para Enviar, adicione outra regra denominada **ReceiveRule** com direitos para *Gerenciar*, *Enviar*, *Escutar* e clique em **Salvar**.

	![][5]

7. Clique na guia **Painel** parte superior da página e clique em **Informações de Conexão**. Copie as duas cadeias de conexão para um local temporário, pois você irá usá-las posteriormente neste tutorial.

	![][6]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Visual Studio, abra o projeto **Destinatário** que você criou anteriormente.

2. Clique com o botão direito do mouse na solução **Destinatário**, clique em **Adicionar**, em seguida, em **Projeto Existente**.
 
3. Localize o arquivo Sender.csproj existente e, em seguida, clique duas vezes nele para adicioná-lo à solução.
 
4. Novamente, clique com o botão direito na solução **Destinatário** e clique em **Propriedades**. A página de propriedades **Destinatário** será exibida.

5. Clique em **Projeto de Inicialização** e, em seguida, clique no botão **Vários projetos de inicialização**. Defina a caixa **Ação** de ambos os projetos **Destinatário** e **Remetente** para **Iniciar**.

	![][19]

6. Clique em **Dependências do Projeto**. Na caixa **Projetos**, clique em **Remetente**. Na caixa **Depende**, verifique se o **Destinatário** está marcado.

	![][20]

7. Clique em **OK** para descartar a caixa de diálogo **Propriedades**.

1.	Pressione F5 para executar o projeto **Destinatário** no Visual Studio e aguarde que ele inicie os destinatários para todas as partições.

	![][21]

2.	O projeto **Remetente** será executado automaticamente. Pressione **Enter** na janela do console e veja os eventos aparecerem na janela do destinatário.

	![][22]

Pressione **Ctrl+C** na janela **Remetente** para encerrar o aplicativo do Remetente e, em seguida, pressione **Enter** na janela do Destinatário para finalizar o aplicativo.

## Próximas etapas

Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

- Um [aplicativo de exemplo completo que usa os Hubs de Evento][].
- O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][].
- Uma [solução de mensagens na fila][] usando filas do Barramento de Serviço.
- [Visão geral de Hubs de Evento][]

<!-- Images. -->
[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[portal clássico do Azure]: https://manage.windowsazure.com/
[Host do Processador de Eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solução de mensagens na fila]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0413_2016-->