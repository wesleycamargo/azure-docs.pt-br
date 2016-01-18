## Processando mensagens de dispositivo para a nuvem

Nesta seção, você criará um aplicativo do console do Windows que processa mensagens do dispositivo para a nuvem do Hub IoT. O Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][Event Hubs Overview] para ler mensagens do dispositivo para a nuvem. Este tutorial usa o [EventProcessorHost] para processar essas mensagens em um aplicativo de console. Para saber mais sobre como processar mensagens dos Hubs de Eventos, consulte o tutorial [Introdução aos Hubs de Eventos].

O principal desafio ao implementar o armazenamento confiável de mensagens de ponto de dados ou ao encaminhar as interativas é que o processamento do evento dos Hubs de Eventos baseia-se no consumidor da mensagem para verificar seu andamento. Além disso, para alcançar a alta taxa de transferência durante a leitura dos Hubs de Eventos, os pontos de verificação deverão acontecer em grandes lotes, criando a possibilidade de processamento duplicado para um grande número de mensagens. Neste tutorial, mostramos como sincronizar gravações de armazenamento do Azure e janelas de eliminação de duplicação do Barramento de Serviço com pontos de verificação do Host de Processador de Eventos.

Para gravar mensagens de forma confiável no armazenamento do Azure, usamos o recurso [blobs de blocos][Azure Block Blobs] de confirmação de bloco individual. O processador de eventos acumula mensagens na memória até o momento de executar um ponto de verificação (isto é, depois que o buffer acumulado for maior do que o tamanho máximo do bloco de 4 Mb, ou depois que a janela de tempo de eliminação de duplicação passar). Então, antes do ponto de verificação, um novo bloco será confirmado no blob.

O processador de evento usa o deslocamento de mensagem dos Hubs de Eventos como ids de bloco. Isso permite executar uma verificação de eliminação de duplicação antes de confirmar o novo bloco de armazenamento, tomando cuidado com uma possível falha entre um bloco confirmado e o ponto de verificação.

> [AZURE.NOTE]Este tutorial usa uma única conta de armazenamento para gravar todas as mensagens recuperadas do Hub IoT. Consulte as [Diretrizes de escalabilidade do Armazenamento do Azure] para decidir se precisa de várias contas do Armazenamento do Azure em sua solução.

Para evitar duplicatas no processamento de mensagens interativas, usamos o recurso de eliminação de duplicação do Barramento de Serviço. Ao carimbar cada mensagem interativa com uma `MessageId` única, o Barramento de Serviço poderá garantir que, na janela de tempo de eliminação de duplicação especificada, duas mensagens com a mesma `MessageId` não sejam entregues aos destinatários. Essa duplicação, junto com a semântica de conclusão por mensagem fornecida pelas filas do Barramento de Serviço, torna o processamento confiável de mensagens interativas significativamente mais fácil.

Para garantir que nenhuma mensagem seja reenviada fora da janela de eliminação de duplicação, podemos sincronizar o mecanismo de ponto de verificação do Host de Processador de Eventos com a janela de eliminação de duplicação de fila do Barramento de Serviço. Isso é realizado pela imposição de um ponto de verificação pelo menos uma vez a cada janela de tempo decorrida (neste tutorial, 1 hora).

> [AZURE.NOTE]Este tutorial usa uma única fila de Barramento de Serviço particionada para processar todas as mensagens interativas recuperadas do Hub IoT. Dependendo da necessidade da solução, consulte [documentação do Barramento de Serviço] para saber mais sobre como usar Filas do Barramento de Serviço.

### Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço
Para usar o [EventProcessorHost], você deverá ter uma conta de Armazenamento do Azure. Você pode usar uma já existente ou seguir as instruções em [Sobre o Armazenamento do Azure] para criar uma nova. Anote a cadeia de conexão da conta de armazenamento.

Você também precisará uma fila do Barramento de Serviço para habilitar o processamento confiável de mensagens interativas. Você pode criar uma fila programaticamente com uma janela de eliminação de duplicação de 1 hora, como explicado em [Como usar filas do Barramento de Serviço][Service Bus Queue] ou usar o [portal do Azure], seguindo essas etapas:

1. Clique em **NOVO** no canto inferior esquerdo, em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Fila**, em **Personalizado**, selecione o nome **d2ctutorial** e selecione uma janela de eliminação de duplicação de 1 hora.

    ![][30]

2. Clique no nome da fila (**d2ctutorial**), em **Configurar** e crie duas políticas de acesso compartilhado, uma chamada **enviar** com permissões **Enviar** e outra chamada **escutar** com permissões **Escutar**. Clique em **Salvar** na parte inferior quando concluir.

    ![][31]

3. Clique em **Painel** na parte superior, em **informações de conexão** na parte inferior, anote as duas cadeias de conexão.

    ![][32]

### Criar o processador de eventos

1. Na atual solução do Visual Studio, clique em **Arquivo->Adicionar->Projeto** para criar um novo projeto de Aplicativo de Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo de Console**. Chame o projeto de **ProcessDeviceToCloudMessages**.

    ![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**.

    A caixa de diálogo **Gerenciar Pacotes NuGet** será exibida.

3. Pesquise por `WindowsAzure.ServiceBus`, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [pacote NuGet do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as suas dependências.

4. Pesquise por `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

5. Clique com o botão direito do mouse no projeto **ProcessDeviceToCloudMessages**, clique em **Adicionar** e clique em **Classe**. Chame a nova classe de **StoreEventProcessor** e clique em **OK** para criar a classe.

6. Adicione as seguintes instruções na parte superior do arquivo StoreEventProcessor.cs:

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    Em seguida, substitua o seguinte código no corpo da classe:

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }

                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    Essa classe será chamada pelo **EventProcessorHost** para processar mensagens do dispositivo para a nuvem recebidas do Hub IoT e implementa a lógica principal para armazenar mensagens de forma confiável em um contêiner de blob e para encaminhar mensagens interativas para a fila do Barramento de Serviço. O método `OpenAsync()` inicializou a variável `currentBlockInitOffset`, que controla o deslocamento atual da primeira mensagem que está sendo lida por este processador de eventos (lembre-se de que cada processador é responsável por uma única partição).

    O método `ProcessEventsAsync()` recebe um lote de mensagens do Hub IoT, que são processadas da maneira a seguir. As mensagens interativas são enviadas para a fila do Barramento de Serviço, enquanto as mensagens de ponto de dados são anexadas ao memoryBuffer chamado `toAppend`. Caso o buffer de memória atinja o limite de bloqueio (isto é, 4 Mb) ou se as janelas de tempo de eliminação de duplicação do Barramento de Serviço tenham passado desde o último ponto de verificação (neste tutorial, 1 hora), um ponto de verificação será disparado.

    O método `AppendAndCheckpoint()` primeiro gera uma blockId para o bloco a ser anexado. Como o Armazenamento do Azure requer que todas as ids de bloco tenham o mesmo comprimento, o deslocamento é preenchido com zeros à esquerda (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Em seguida, se um bloco com essa id já estiver no blob, o método o substituirá pelo atual.

> [AZURE.NOTE]Para simplificar o código, este tutorial usa um arquivo único de blob por partição para armazenar as mensagens. Uma solução real implementaria o arquivo sem interrupção, criando arquivos adicionais quando um determinado tamanho for atingido (observe que o blob do Azure pode ter, no máximo, 195 Gb), ou após um determinado período (por exemplo, `fileName_{partitionId}_{date-time}`).

7. Na classe **Programa**, adicione as seguintes instruções `using` na parte superior:

        using Microsoft.ServiceBus.Messaging;

    Então, modifique o método **Main** para a classe **Program** como mostrado abaixo, substituindo a cadeia de conexão **iothubowner** do Hub IoT (do tutorial [Introdução ao Hub IoT]), a cadeia de conexão de armazenamento e a cadeia de conexão do Barramento de Serviço com permissões **Enviar** para a fila chamada **d2ctutorial**:

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]Para simplificar, este tutorial usa uma única instância do [EventProcessorHost]. Consulte o [Guia de programação de Hubs de Eventos] e o tutorial [Processar mensagens do dispositivo para a nuvem] para obter mais informações sobre o processamento de mensagens do dispositivo para a nuvem.

## Receber mensagens interativas
Nesta seção, você escreverá um aplicativo de console do Windows que recebe mensagens interativas da fila do Barramento de Serviço. Consulte [Criar aplicativos multicamadas com o Barramento de Serviço][] para saber mais sobre como projetar uma solução usando o Barramento de Serviço.

1. Na atual solução do Visual Studio, crie um novo projeto de Aplicativo de Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo de Console**. Chame o projeto de **ProcessD2cInteractiveMessages**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**.

    Isso exibe a janela Gerenciar Pacotes NuGet.

3. Pesquise por `WindowsAzure.Service Bus`, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as dependências.

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Por fim, adicione as linhas a seguir ao método **Main**, substituindo a cadeia de conexão por permissões **Escutar** para a fila chamada **d2ctutorial**:

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[Sobre o Armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Introdução aos Hubs de Eventos]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Diretrizes de escalabilidade do Armazenamento do Azure]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[documentação do Barramento de Serviço]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Guia de programação de Hubs de Eventos]: ../event-hubs/event-hubs-programming-guide.md

[Azure preview portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[portal do Azure]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Criar aplicativos multicamadas com o Barramento de Serviço]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md


<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=Nov15_HO3-->