<properties
	pageTitle="Processar mensagens de dispositivo para nuvem de Hub IoT | Microsoft Azure"
	description="Siga este tutorial para aprender os padrões úteis para processar as mensagens dispositivo para nuvem Hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/19/2016"
     ms.author="dobett"/>

# Tutorial: Como processar mensagens de dispositivo para nuvem do Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Outros tutoriais ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d]) mostram como usar a funcionalidade básica de mensagem do dispositivo para nuvem e da nuvem para dispositivo do Hub IoT.

Esse tutorial se baseia no código mostrado no tutorial [Introdução ao Hub IoT] e mostra dois padrões escalonáveis que você pode usar para processar mensagens de dispositivo para nuvem:

- O armazenamento confiável de mensagens do dispositivo para a nuvem no [Armazenamento de Blobs do Azure]. Um cenário muito comum é a análise de *caminho frio*, no qual você armazena dados de telemetria em blobs para usar como entrada em processo analíticos. Esses processos podem ser orientados por ferramentas como [Azure Data Factory] ou a pilha [HDInsight (Hadoop)].

- O processamento confiável de mensagens *interativas* do dispositivo para nuvem. As mensagens do dispositivo para nuvem são interativas quando disparadas imediatamente para um conjunto de ações no back-end do aplicativo. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM. Por outro lado, as mensagens de *ponto de dados* simplesmente são alimentadas no mecanismo de análise. Por exemplo, a telemetria de temperatura de um dispositivo que deve ser armazenado para análise posterior é uma mensagem de ponto de dados.

Como o Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs] para receber mensagens de dispositivo para nuvem, este tutorial usa uma instância [EventProcessorHost]. Esta instância:

* Armazena com segurança as mensagens do *ponto de dados* no armazenamento de blobs do Azure.
* Encaminha mensagens *interativas* do dispositivo para a nuvem a uma [Fila do Barramento de Serviço] do Azure para processamento imediato.

O Barramento de Serviço ajuda a garantir processamento confiável de mensagens interativas, pois fornece pontos de verificação por mensagem e eliminação de duplicação baseada em janela de tempo.

> [AZURE.NOTE] Uma instância **EventProcessorHost** é apenas uma maneira de processar mensagens interativas. Outras opções incluem [Service Fabric Azure][lnk-service-fabric] e [Stream Analytics do Azure][lnk-stream-analytics].

Ao final deste tutorial, você executará três aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT], que envia mensagens de ponto de dados do dispositivo para nuvem a cada segundo e mensagens interativas do dispositivo para nuvem a cada 10 segundos. Este aplicativo usa o protocolo AMQPS para se comunicar com o Hub IoT.
* **ProcessDeviceToCloudMessages** usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com os Hubs de Eventos. Desse modo, ele armazena confiavelmente mensagens de pontos de dados no armazenamento de blobs do Azure e encaminha mensagens interativas a uma fila do Barramento de Serviço.
* **ProcessD2CInteractiveMessages** remove da fila as mensagens interativas da fila do Barramento de Serviço.

> [AZURE.NOTE] O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções passo a passo sobre como substituir o dispositivo simulado neste tutorial por um dispositivo físico e, de modo geral, como conectar dispositivos a um Hub IoT, confira o [Centro de desenvolvedores do Azure IoT].

Esse tutorial se aplica diretamente a outras formas de consumir mensagens compatíveis com Hubs de Eventos, como projetos do [HDInsight (Hadoop)]. Para obter mais informações,confira [Guia do desenvolvedor do Hub IoT do Azure — dispositivo para nuvem].

Para concluir esse tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].


## Enviar mensagens interativas de um dispositivo simulado

Nesta seção, você modificará o aplicativo do dispositivo simulado que você criou no tutorial [Introdução ao Hub IoT] para enviar mensagens interativas do dispositivo para a nuvem ao Hub IoT.

1. No Visual Studio, no projeto **SimulatedDevice**, adicione o método a seguir à classe **Program**.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Esse método é muito semelhante ao método **SendDeviceToCloudMessagesAsync** no projeto **SimulatedDevice**. A única diferença é que agora você define a propriedade do sistema **MessageId** e uma propriedade de usuário chamada **messageType**. O código atribui um GUID (identificador global exclusivo) para a propriedade **MessageId**. O Barramento de Serviço pode usá-lo para eliminar a duplicação de mensagens recebidas. O exemplo usa a propriedade **messageType** para distinguir as mensagens interativas das de ponto de dados. Essas informações são transmitidas nas propriedades da mensagem pelo aplicativo, em vez de no corpo da mensagem, para que o processador de evento não precise desserializar a mensagem apenas para executar o roteamento.

    > [AZURE.NOTE] É importante criar o **MessageId** usado para eliminar a duplicação de mensagens interativas no código do dispositivo. Comunicações de rede intermitente ou outras falhas pode resultar em várias retransmissões da mesma mensagem desse dispositivo. Você também pode usar uma ID de mensagem semântica, como um hash dos campos de dados da mensagem relevante, no lugar de um GUID.

2. Adicione o seguinte método ao método **Main**, logo antes da linha `Console.ReadLine()`:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Para simplificar, esse tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como uma retirada exponencial, como sugerido no artigo [Tratamento de falhas transitórias] do MSDN.

## Processar mensagens de dispositivo para nuvem

Nesta seção, você criará um aplicativo do console do Windows que processa mensagens do dispositivo para a nuvem do Hub IoT. Um Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos] para permitir que um aplicativo leia mensagens do dispositivo para a nuvem. Este tutorial usa a classe [EventProcessorHost] para processar essas mensagens em um aplicativo de console. Para obter mais informações sobre como processar as mensagens dos Hubs de Eventos, confira o tutorial [Introdução aos Hubs de Eventos].

Quando você implementa o armazenamento confiável de mensagens de ponto de dados ou ao encaminhar mensagens interativas, o principal desafio é que o processamento do evento dos Hubs de Eventos baseia-se no consumidor da mensagem para fornecer pontos de verificação para seu andamento. Além disso, para alcançar uma alta taxa de transferência ao ler de Hubs de eventos é necessário fornecer um ponto de verificação em lotes grandes. Isso cria a possibilidade de processamento duplicado para um grande número de mensagens se houver uma falha e de reverter para o ponto de verificação anterior. Neste tutorial, você verá como sincronizar gravações de armazenamento do Azure e janelas de eliminação de duplicação do Barramento de Serviço com pontos de verificação do **EventProcessorHost**.

Para gravar mensagens no armazenamento do Azure de forma confiável, o exemplo usa o recurso [blobs de blocos][Azure Block Blobs] de confirmação de bloco individual. O processador de eventos acumula mensagens na memória até o momento de fornecer um ponto de verificação, como quando o buffer acumulado for maior do que o tamanho máximo do bloco de 4 MB, ou depois que a janela de tempo de eliminação de duplicação do Barramento de Serviço passar. Então, antes do ponto de verificação, o código confirma novo bloco no blob.

O processador de evento usa o deslocamento de mensagem dos Hubs de Eventos como IDs de bloco. Isso permite executar uma verificação de eliminação de duplicação antes de confirmar o novo bloco de armazenamento, tomando cuidado com uma possível falha entre a confirmação de um bloco e o ponto de verificação.

> [AZURE.NOTE] Este tutorial usa uma única conta de armazenamento para gravar todas as mensagens recuperadas do Hub IoT. Para decidir se você precisará usar várias contas de armazenamento do Azure em sua solução, confira [Azure Storage scalability Guidelines] (Diretrizes de escalabilidade do Armazenamento do Azure).

O aplicativo utiliza o Barramento de Serviço do recurso de eliminação de duplicação para evitar duplicatas quando processa mensagens interativas. O dispositivo simulado carimba cada mensagem interativa com uma única **MessageId**. Isso permite que o Barramento de Serviço possa garantir que, na janela de tempo de eliminação de duplicação especificada, duas mensagens com a mesma **MessageId** não sejam entregues aos destinatários. Essa eliminação de duplicação, junto com a semântica de conclusão por mensagem fornecida pelas filas do Barramento de Serviço, facilita o processamento confiável de mensagens interativas.

Para garantir que nenhuma mensagem seja reenviada fora da janela de eliminação de duplicação, o código sincroniza o mecanismo de ponto de verificação **EventProcessorHost** com a janela de eliminação de duplicação de fila do Barramento de Serviço. Isso é realizado pela imposição de um ponto de verificação pelo menos uma vez sempre que o tempo da janela de eliminação de duplicação decorrer (neste tutorial, a janela é de uma hora).

> [AZURE.NOTE] Este tutorial usa uma única fila de Barramento de Serviço particionada para processar todas as mensagens interativas recuperadas do Hub IoT. Para obter mais informações sobre como usar filas do Barramento de Serviço para atender aos requisitos de escalabilidade da sua solução, confira a documentação [Barramento de Serviço do Azure].

### Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço
Para usar a classe [EventProcessorHost], você deve ter uma conta de armazenamento do Azure para habilitar o **EventProcessorHost** para registro do ponto de verificação. Você pode usar uma conta de armazenamento existente ou seguir as instruções em [Sobre o Armazenamento do Azure] para criar uma nova. Anote a cadeia de conexão da conta de armazenamento.

> [AZURE.NOTE] Ao copiar e colar a cadeia de conexão da conta de armazenamento, verifique se não há nenhum espaço incluído.

Você também precisará de uma fila do Barramento de Serviço para habilitar o processamento confiável de mensagens interativas. Você pode criar uma fila programaticamente com uma janela de eliminação de duplicação de uma hora, como explicado em [Como usar filas do Barramento de Serviço][Service Bus queue]. Como alternativa, você pode usar o [portal clássico do Azure][lnk-classic-portal] seguindo estas etapas:

1. Clique em **Novo** no canto inferior esquerdo. Em seguida, clique em **Serviços de Aplicativos** > **Barramento de Serviço** > **Fila** > **Criação Personalizada**. Insira o nome **d2ctutorial**, selecione uma região e use um namespace existente ou crie um novo. Na página seguinte, selecione **Habilitar detecção de duplicidades** e defina a **Janela de tempo do histórico de detecção de duplicidades** como uma hora. Em seguida, clique na marca de seleção no canto inferior direito para salvar a configuração de fila.

    ![Criar uma fila usando o portal do Azure][30]

2. Na lista de filas do Barramento de Serviço, clique em **d2ctutorial** e, em seguida, clique em **Configurar**. Crie duas políticas de acesso compartilhado, uma chamada **enviar** com permissões de **Envio** e outra chamada **escutar** com permissões de **Escuta**. Quando terminar, clique em **Salvar** na parte inferior.

    ![Configurar uma fila no portal do Azure][31]

3. Clique em **Painel** na parte superior e em **Informações de conexão** na parte inferior. Anote as duas cadeias de conexão.

    ![Painel de fila no portal do Azure][32]

### Criar o processador de eventos

1. Na atual solução do Visual Studio, para criar um novo projeto de Visual C# do Windows usando o modelo de projeto do **Aplicativo do Console**, clique em **Arquivo** > **Adicionar** > **Novo Projeto**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Chame o projeto de **ProcessDeviceToCloudMessages** e clique em **OK**.

    ![Novo projeto no Visual Studio][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProcessDeviceToCloudMessages** e clique em **Gerenciar Pacotes NuGet**. A caixa de diálogo **Gerenciador de Pacotes NuGet** será exibida.

3. Pesquise por **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [pacote NuGet do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as suas dependências.

4. Pesquise por **Hub de Evento do Barramento de Serviço do Microsoft Azure - EventProcessorHost**, clique em **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

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

    A classe **EventProcessorHost** chama essa classe para processar mensagens de dispositivo para nuvem recebidas do Hub IoT. O código nessa classe implementa a lógica para armazenar mensagens de forma confiável em um contêiner de blob e encaminhar mensagens interativas para a fila do Barramento de Serviço.

    O método **OpenAsync** inicializa a variável **currentBlockInitOffset**, que controla o deslocamento atual da primeira mensagem lida por esse processador de eventos. Lembre-se de que cada processador é responsável por uma única partição.

    O método **ProcessEventsAsync** recebe um lote de mensagens do Hub IoT e o processa da seguinte maneira: ele envia mensagens interativas para a fila do Barramento de Serviço e anexa as mensagens de ponto de dados no buffer de memória denominado **toAppend**. Caso o buffer de memória atinja o limite de bloqueio de 4 MB ou se as janelas de tempo de eliminação de duplicação do Barramento de Serviço tenham passado desde o último ponto de verificação (neste tutorial, uma hora), um ponto de verificação será disparado.

    O método **AppendAndCheckpoint** primeiro gera uma blockId para o bloco a ser anexado. O Armazenamento do Azure requer que todas as IDs de bloco tenham o mesmo comprimento, então o método preenche o deslocamento com zeros à esquerda - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Em seguida, se um bloco com essa ID já estiver no blob, o método o substituirá pelo atual conteúdo do buffer.

    > [AZURE.NOTE] Para simplificar o código, este tutorial usa um arquivo único de blob por partição para armazenar as mensagens. Uma solução real implementaria o arquivo sem interrupção, criando arquivos adicionais após um determinado período ou quando um determinado tamanho fosse atingido (observe que o blob de blocos do Azure pode ter, no máximo, 195 GB).

8. Na classe **Program**, adicione a seguinte instrução **using** à parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifique o método **Main** na classe **Program**, conforme mostrado abaixo. Substitua a cadeia de conexão **iothubowner** do Hub IoT (do tutorial [Introdução ao Hub IoT]), a cadeia de conexão de armazenamento e a cadeia de conexão do Barramento de Serviço com permissões de **Envio** para a fila chamada **d2ctutorial**:

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

    > [AZURE.NOTE] Para simplificar, este tutorial usa uma única instância da classe [EventProcessorHost]. Para obter mais informações, confira o [Guia de programação de Hubs de Eventos].

## Receber mensagens interativas
Nesta seção, você escreverá um aplicativo de console do Windows que recebe mensagens interativas da fila do Barramento de Serviço. Para obter mais informações sobre como arquitetar uma solução usando o Barramento de Serviço, confira [Build multi-tier applications with Service][] (Compilar aplicativos multicamadas com o Barramento de Serviço).

1. Na atual solução do Visual Studio, crie um novo projeto do Visual C# do Windows usando o modelo de projeto do **Aplicativo do Console**. Chame o projeto de **ProcessD2CInteractiveMessages**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProcessD2CInteractiveMessages** e clique em **Gerenciar Pacotes NuGet**. Isso faz com que a janela **Gerenciador de Pacotes NuGet** seja exibida.

3. Pesquise por **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) com todas as dependências.

4. Adicione a seguinte instrução **using** à parte superior do arquivo **Program.cs**:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por fim, adicione as seguintes linhas ao método **Main**. Substitua a cadeia de conexão por permissões **Escutar** para a fila chamada **d2ctutorial**:

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

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, no Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização** e, em seguida, **Iniciar** como a ação dos projetos **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Pressione **F5** para iniciar os três aplicativos de console. O aplicativo **ProcessD2CInteractiveMessages** deve processar cada mensagem interativa enviada do aplicativo **SimulatedDevice**.

  ![Três aplicativos de console][50]

> [AZURE.NOTE] Para ver as atualizações em seu arquivo de blob, talvez seja necessário reduzir a constante **MAX\_BLOCK\_SIZE** na classe **StoreEventProcessor** para um valor menor, como **1024**. Isso ocorre porque leva algum tempo para alcançar o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho de bloco menor, você não precisa esperar muito tempo para ver o blob ser criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais dimensionável.

## Próximas etapas

Neste tutorial, você aprendeu a processar com segurança as mensagens de ponto de dados e interativas do dispositivo para nuvem usando a classe [EventProcessorHost].

O tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d] mostra como enviar mensagens para seus dispositivos de back-end.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Armazenamento de Blobs do Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[Fila do Barramento de Serviço]: ../service-bus/service-bus-dotnet-get-started-with-queues.md

[Guia do desenvolvedor do Hub IoT do Azure — dispositivo para nuvem]: iot-hub-devguide.md#d2c

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Sobre o Armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introdução aos Hubs de Eventos]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Hubs de Eventos]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guia de programação de Hubs de Eventos]: ../event-hubs/event-hubs-programming-guide.md
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Build multi-tier applications with Service]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0831_2016-->