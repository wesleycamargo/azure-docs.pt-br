## Receber mensagens com EventProcessorHost

**EventProcessorHost** é uma classe .NET que simplifica o recebimento de eventos de Hubs de Eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos de Hubs de Eventos. Usando o **EventProcessorHost**, você pode dividir eventos em vários destinatários, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o **EventProcessorHost** para um único destinatário. A [amostra de processamento de eventos em escala] mostra como usar o **EventProcessorHost** com vários destinatários.

Para mais informações sobre padrões de recebimento de Hubs de Evento, consulte o [Guia do desenvolvedor de Hubs de Evento].

[EventProcessorHost] é uma classe .NET que simplifica o recebimento de eventos de Hubs de Eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses Hubs de Eventos. Usando o [EventProcessorHost], você pode dividir eventos em vários destinatários, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o [EventProcessorHost] para um único destinatário. A [amostra de processamento de eventos em escala] mostra como usar o [EventProcessorHost] com vários destinatários.

Para usar o [EventProcessorHost], você deve ter uma [conta de armazenamento do Azure]:

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Dados**, em **Armazenamento**, em **Criação rápida** e, em seguida, digite um nome para sua conta de armazenamento. Selecione a região desejada e, em seguida, clique em **Criar conta de armazenamento**.

   	![][11]

3. Clique na conta de armazenamento criada recentemente e, em seguida, clique em **Gerenciar chaves de acesso**:

   	![][12]

	Copie a chave de acesso para uso posterior.

4. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console.** Nomeie o projeto como **Destinatário**.

   	![][14]

5. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet**. 

	A caixa de diálogo **Gerenciar pacotes NuGet** será exibida.

6. Pesquise por `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, clique em **Instalar** e aceite os termos de uso. 

	![][13]

	Isso faz o download, instala e adiciona uma referência para o <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">Hub de Evento do Barramento de Serviço do Azure - Pacote NuGet do EventProcessorHost</a>, com todas as suas dependências.

7. Crie uma nova classe chamada **SimpleEventProcessor**, adicione as seguintes instruções na parte superior do arquivo:

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	Em seguida, insira o seguinte código no corpo da classe:

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	        
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine(string.Format("Processor Shuting Down.  Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason.ToString()));
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine(string.Format("SimpleEventProcessor initialize.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset));
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	                
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5)) 
                { 
                    await context.CheckpointAsync(); 
                    this.checkpointStopWatch.Restart(); 
                } 
	        }
	    }

	Essa classe será chamada pelo **EventProcessorHost** para processar eventos recebidos do Hub de Eventos. Observe que a classe `SimpleEventProcessor` usa um cronômetro para chamar o método de ponto de verificação periodicamente no contexto do **EventProcessorHost**. Isso garante que, se o destinatário for reiniciado, ele perderá, no máximo, cinco minutos de trabalho de processamento.

8. Na classe **Programa**, adicione as seguintes instruções `using` na parte superior:

		using Microsoft.ServiceBus.Messaging;
		using System.Threading.Tasks;
	
	Em seguida, adicione o seguinte código no método **Main** , substituindo a cadeia de conexão e o nome do Hub de eventos e a conta de armazenamento e a chave que você copiou na seção anterior:

		string eventHubConnectionString = "{event hub connection string}";
        string eventHubName = "{event hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                    storageAccountName, storageAccountKey);

        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();
            
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

> [AZURE.NOTE] Este tutorial usa uma única instância do [EventProcessorHost]. Para aumentar a taxa de transferência, é recomendável que você execute várias instâncias do [EventProcessorHost], conforme mostrado [Amostra de processamento de eventos em escala]. Nesses casos, as diversas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. Se você quiser vários destinatários para cada processo dos eventos *all*, você deve usar o conceito de **ConsumerGroup**. Ao receber eventos em máquinas diferentes, pode ser útil especificar nomes para instâncias de [EventProcessorHost] com base em máquinas (ou funções) nas quais eles foram implantados. Para obter mais informações sobre esses tópicos, consulte a [Visão geral de Hubs de Evento] e o [Guia de programação de Hubs de Eventos].

<!-- Links -->
[Visão Geral de Hubs de Evento]: http://msdn.microsoft.com/library/azure/dn821413.aspx
[amostra de processamento de eventos em escala]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Conta de armazenamento do Azure]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[Guia do desenvolvedor de Hubs de Evento]: http://msdn.microsoft.com/library/azure/dn789972.aspx
<!--HONumber=42-->
