## Processando mensagens de dispositivo para a nuvem

Nesta seção, você criará um aplicativo do console do Windows que processa mensagens do dispositivo para a nuvem do Hub IoT. Um Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos] para permitir que um aplicativo leia mensagens do dispositivo para a nuvem. Este tutorial usa a classe [EventProcessorHost] para processar essas mensagens em um aplicativo de console. Para saber mais sobre como processar mensagens dos Hubs de Eventos, consulte o tutorial [Introdução aos Hubs de Eventos].

O principal desafio ao implementar o armazenamento confiável de mensagens de ponto de dados ou ao encaminhar as mensagens interativas é que o processamento do evento dos Hubs de Eventos baseia-se no consumidor da mensagem para verificar seu andamento. Além disso, para alcançar uma alta taxa de transferência ao ler de Hubs de eventos é necessário criar um ponto de verificação em lotes grandes. Isso cria a possibilidade de processamento duplicado para um grande número de mensagens se houver uma falha e de reverter para o ponto de verificação anterior. Neste tutorial, você verá como sincronizar gravações de armazenamento do Azure e janelas de eliminação de duplicação do Barramento de Serviço com pontos de verificação do **EventProcessorHost**.

Para gravar mensagens de forma confiável no armazenamento do Azure, o exemplo o recurso [blobs de blocos][Azure Block Blobs] de confirmação de bloco individual. O processador de eventos acumula mensagens na memória até o momento de executar um ponto de verificação, como quando o buffer acumulado for maior do que o tamanho máximo do bloco de 4 Mb, ou depois que a janela de tempo de eliminação de duplicação do Barramento de Serviço passar. Então, antes do ponto de verificação, o código confirma novo bloco no blob.

O processador de evento usa o deslocamento de mensagem dos Hubs de Eventos como ids de bloco. Isso permite executar uma verificação de eliminação de duplicação antes de confirmar o novo bloco de armazenamento, tomando cuidado com uma possível falha entre a confirmação de um bloco e o ponto de verificação.

> [AZURE.NOTE] Este tutorial usa uma única conta de armazenamento para gravar todas as mensagens recuperadas do Hub IoT. Veja as [Diretrizes de escalabilidade do Armazenamento do Azure] para decidir se precisará usar várias contas do Armazenamento do Azure em sua solução.

O aplicativo utiliza o Barramento de Serviço do recurso de eliminação de duplicação para evitar duplicatas quando processa mensagens interativas. O dispositivo simulado carimba cada mensagem interativa com uma única **MessageId**, assim o Barramento de Serviço poderá garantir que, na janela de tempo de eliminação de duplicação especificada, duas mensagens com a mesma **MessageId** não sejam entregues aos destinatários. Essa eliminação de duplicação, junto com a semântica de conclusão por mensagem fornecida pelas filas do Barramento de Serviço, facilita o processamento confiável de mensagens interativas.

Para garantir que nenhuma mensagem seja reenviada fora da janela de eliminação de duplicação, podemos sincronizar o mecanismo de ponto de verificação **EventProcessorHost** com a janela de eliminação de duplicação de fila do Barramento de Serviço. Isso é realizado pela imposição de um ponto de verificação pelo menos uma vez sempre que o tempo da janela de eliminação de duplicação decorrer (neste tutorial, 1 hora).

> [AZURE.NOTE] Este tutorial usa uma única fila de Barramento de Serviço particionada para processar todas as mensagens interativas recuperadas do Hub IoT. Consulte a [documentação do Barramento de Serviço] para saber mais sobre como usar Filas do Barramento de Serviço para atender aos requisitos de escalabilidade da sua solução.

### Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço
Para usar a classe [EventProcessorHost], você deve ter uma conta de Armazenamento do Azure para habilitar o **EventProcessorHost** para registro do ponto de verificação. Você pode usar uma conta de armazenamento existente ou seguir as instruções em [Sobre o Armazenamento do Azure] para criar uma nova. Anote a cadeia de conexão da conta de armazenamento.

> [AZURE.NOTE] Ao copiar e colar a cadeia de conexão da conta de armazenamento, verifique se não há nenhum espaço incluído na cadeia de conexão.

Você também precisará de uma fila do Barramento de Serviço para habilitar o processamento confiável de mensagens interativas. Você pode criar uma fila de forma programática com uma janela de eliminação de duplicação de uma hora, como explicado na seção [Como usar filas do Barramento de Serviço][Service Bus Queue], ou usar o [portal clássico do Azure] seguindo estas etapas:

1. Clique em **NOVO** no canto inferior esquerdo, depois, clique em **Serviços de Aplicativos**, em seguida, em **Barramento de Serviço**, em **Fila** e, em seguida, em **Criação personalizada**, insira o nome **d2ctutorial**, selecione uma região, use um namespace existente ou crie um novo e, na página seguinte, selecione **Habilitar detecção de duplicidades** e defina a **Janela de tempo do histórico de detecção de duplicidades** para uma hora. Em seguida, clique na marca de seleção para salvar a sua configuração de fila.

    ![][30]

2. Na lista de filas do Barramento de Serviço, clique em **d2ctutorial** e, em seguida, clique em **Configurar**. Crie duas políticas de acesso compartilhado, uma chamada **enviar** com permissões de **Envio** e outra chamada **escutar** com permissões de **Escuta**. Clique em **Salvar** na parte inferior quando concluir.

    ![][31]

3. Clique em **Painel** na parte superior, em **Informações de conexão** na parte inferior, anote as duas cadeias de conexão.

    ![][32]

### Criar o processador de eventos

1. Na atual solução do Visual Studio, clique em **Arquivo**, em seguida, **Adicionar** e, em seguida, **Novo Projeto** para criar um novo projeto de Visual C# do Windows usando o modelo de projeto do **Aplicativo do Console**. Verifique se a versão do .NET Framework é a 4.5.1 ou posterior. Chame o projeto de **ProcessDeviceToCloudMessages**.

    ![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProcessDeviceToCloudMessages** e clique em **Gerenciar Pacotes NuGet**. A caixa de diálogo **Gerenciador de Pacotes NuGet** será exibida.

3. Pesquise por **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [pacote NuGet do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as suas dependências.

4. Pesquise por **'Hub de Evento do Barramento de Serviço do Microsoft Azure - EventProcessorHost'**, clique em **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

5. Clique com o botão direito do mouse no projeto **ProcessDeviceToCloudMessages**, clique em **Adicionar** e clique em **Classe**. Chame a nova classe de **StoreEventProcessor** e clique em **OK** para criar a classe.

6. Adicione as seguintes instruções na parte superior do arquivo StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Substitua o seguinte código no corpo da classe:

    ```
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
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
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
    ```

    A classe **EventProcessorHost** chama essa classe para processar mensagens de dispositivo para nuvem recebidas do Hub IoT. O código nessa classe implementa a lógica para armazenar mensagens de forma confiável em um contêiner de blob e encaminhar mensagens interativas para a fila do Barramento de Serviço. O método **OpenAsync**, inicializa a variável **currentBlockInitOffset** que controla o deslocamento atual da primeira mensagem lida por esse processador de eventos. Lembre-se de que cada processador é responsável por uma única partição.
    
    O método **ProcessEventsAsync** recebe um lote de mensagens do Hub IoT e processa-os da seguinte maneira: ele envia mensagens interativas para a fila do Barramento de Serviço e anexa mensagens de ponto de dados no buffer de memória denominado **toAppend**. Caso o buffer de memória atinja o limite de bloqueio de 4 Mb ou se as janelas de tempo de eliminação de duplicação do Barramento de Serviço tenham passado desde o último ponto de verificação (neste tutorial, 1 hora), um ponto de verificação será disparado.

    O método **AppendAndCheckpoint** primeiro gera uma blockId para o bloco a ser anexado. O Armazenamento do Azure requer que todas as ids de bloco tenham o mesmo comprimento, então o método preenche o deslocamento com zeros à esquerda - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Em seguida, se um bloco com essa id já estiver no blob, o método o substituirá pelo atual conteúdo do buffer..

    > [AZURE.NOTE] Para simplificar o código, este tutorial usa um arquivo único de blob por partição para armazenar as mensagens. Uma solução real implementaria o arquivo sem interrupção, criando arquivos adicionais quando um determinado tamanho for atingido (observe que o blob de blocos do Azure pode ter, no máximo, 195 Gb), ou após um determinado período.

8. Na classe **Programa**, adicione as seguintes instruções **de uso** na parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifique o método **Principal** na classe **Programa**, como mostrado abaixo, substituindo a cadeia de conexão **iothubowner** do Hub IoT (do tutorial [Introdução ao Hub IoT]), a cadeia de conexão de armazenamento e a cadeia de conexão do Barramento de Serviço com permissões **Enviar** para a fila chamada **d2ctutorial**:

    ```
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
    ```
    
    > [AZURE.NOTE] Para simplificar, este tutorial usa uma única instância da classe [EventProcessorHost]. Veja o [Guia de programação dos Hubs de Eventos] para obter mais informações.

## Receber mensagens interativas
Nesta seção, você escreverá um aplicativo de console do Windows que recebe mensagens interativas da fila do Barramento de Serviço. Consulte [Criar aplicativos multicamadas com o Barramento de Serviço][] para saber mais sobre como projetar uma solução usando o Barramento de Serviço.

1. Na atual solução do Visual Studio, crie um novo projeto de Visual C# do Windows usando o modelo de projeto do **Aplicativo do Console**. Chame o projeto de **ProcessD2CInteractiveMessages**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProcessD2CInteractiveMessages** e clique em **Gerenciar Pacotes NuGet**. Isso faz com que a janela **Gerenciador de Pacotes NuGet** seja exibida.

3. Pesquise por **WindowsAzure.Service Bus**, clique em **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) com todas as dependências.

4. Adicione a seguinte instrução **de uso** no topo do arquivo **Program.cs**:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por fim, adicione as linhas a seguir ao método **Main**, substituindo a cadeia de conexão por permissões **Escutar** para a fila chamada **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

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
    ```

<!-- Links -->
[Sobre o Armazenamento do Azure]: ../articles/storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Introdução aos Hubs de Eventos]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[Diretrizes de escalabilidade do Armazenamento do Azure]: ../articles/storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Hubs de Eventos]: ../articles/event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guia de programação dos Hubs de Eventos]: ../articles/event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://manage.windowsazure.com/
[Service Bus Queue]: ../articles/service-bus/service-bus-dotnet-how-to-use-queues.md
[Criar aplicativos multicamadas com o Barramento de Serviço]: ../articles/service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[Introdução ao Hub IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[documentação do Barramento de Serviço]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

