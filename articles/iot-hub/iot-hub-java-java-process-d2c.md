---
title: Processar as mensagens do dispositivo para a nuvem do Hub IoT (Java) | Microsoft Docs
description: Siga este tutorial do Java para aprender os padrões úteis para processar as mensagens do dispositivo para a nuvem do Hub IoT.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2016
ms.author: dobett

---
# <a name="tutorial-how-to-process-iot-hub-devicetocloud-messages-using-java"></a>Tutorial: como processar as mensagens do dispositivo para a nuvem do Hub IoT usando o Java
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Outros tutoriais ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d]) mostram como usar a funcionalidade básica de mensagem do dispositivo para nuvem e da nuvem para o dispositivo do Hub IoT.

Esse tutorial se baseia no código mostrado no tutorial [Introdução ao Hub IoT] e mostra dois padrões escalonáveis que você pode usar para processar mensagens de dispositivo para nuvem:

* O armazenamento confiável de mensagens do dispositivo para a nuvem no [Armazenamento de Blobs do Azure]. Um cenário comum é a análise de *caminho frio* , em que você armazena os dados de telemetria em blobs para usar como entrada em processos analíticos. Esses processos podem ser orientados por ferramentas como [Azure Data Factory] ou a pilha [HDInsight (Hadoop)]..
* O processamento confiável de mensagens *interativas* do dispositivo para nuvem. As mensagens do dispositivo para nuvem são interativas quando disparadas imediatamente para um conjunto de ações no back-end do aplicativo. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM. Por outro lado, as mensagens de *ponto de dados* simplesmente são alimentadas no mecanismo de análise. Por exemplo, a telemetria de temperatura de um dispositivo que deve ser armazenado para análise posterior é uma mensagem de ponto de dados.

Como o Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs] para receber mensagens de dispositivo para nuvem, este tutorial usa uma instância [EventProcessorHost]. Esta instância:

* Armazena as mensagens do *ponto de dados* no Armazenamento de Blobs do Azure com segurança.
* Encaminha mensagens *interativas* do dispositivo para a nuvem a uma [Fila do Barramento de Serviço] do Azure para processamento imediato.

O Barramento de Serviço ajuda a garantir processamento confiável de mensagens interativas, pois fornece pontos de verificação por mensagem e eliminação de duplicação baseada em janela de tempo.

> [!NOTE]
> Uma instância **EventProcessorHost** é apenas uma maneira de processar mensagens interativas. Outras opções incluem [Service Fabric Azure][lnk-service-fabric] e [Stream Analytics do Azure][lnk-stream-analytics].
> 
> 

Ao fim deste tutorial, você executará três aplicativos de console do Java:

* **simulated-device**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT] , que envia mensagens de ponto de dados do dispositivo para a nuvem a cada segundo e mensagens interativas do dispositivo para a nuvem a cada 10 segundos. Este aplicativo usa o protocolo AMQPS para se comunicar com o Hub IoT.
* **process-d2c-messages** , que usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com os Hubs de Eventos. Desse modo, ele armazena as mensagens de pontos de dados no Armazenamento de Blobs do Azure de maneira confiável e encaminha as mensagens interativas a uma fila do Barramento de Serviço.
* **process-interactive-messages** , que remove as mensagens interativas da fila do Barramento de Serviço.

> [!NOTE]
> O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo simulado neste tutorial por um dispositivo físico e como conectar dispositivos a um Hub IoT, confira o [Centro de desenvolvedores do Azure IoT].
> 
> 

Esse tutorial se aplica diretamente a outras formas de consumir mensagens compatíveis com Hubs de Eventos, como projetos do [HDInsight (Hadoop)] . Para obter mais informações,confira [Guia do desenvolvedor do Hub IoT do Azure — dispositivo para nuvem].

Para concluir este tutorial, você precisará do seguinte:

* Uma versão de trabalho completa do tutorial [Introdução ao Hub IoT] .
* Java SE 8. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.
* Maven 3.  <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Maven para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. <br/>Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].

## <a name="send-interactive-messages-from-a-simulated-device"></a>Enviar mensagens interativas de um dispositivo simulado
Nesta seção, você modificará o aplicativo do dispositivo simulado que você criou no tutorial [Introdução ao Hub IoT] para enviar mensagens interativas do dispositivo para a nuvem ao Hub IoT.

1. Use um editor de texto para abrir o arquivo simulated-device\src\main\java\com\mycompany\app\App.java. Este arquivo contém o código do aplicativo **simulated-device** que você criou no tutorial [Introdução ao Hub IoT] .
2. Adicione a classe aninhada a seguir à classe **App** :
   
    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);
   
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
   
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```
   
    Essa classe é semelhante à classe **MessageSender** no projeto **simulated-device**. A única diferença é que agora você define a propriedade do sistema **MessageId** e uma propriedade personalizada chamada **messageType**.
    O código atribui um UUID (identificador universal exclusivo) para a propriedade **MessageId** . O Barramento de Serviço pode usar este identificador para eliminar a duplicação das mensagens recebidas. O exemplo usa a propriedade **messageType** para distinguir as mensagens interativas das de ponto de dados. Essas informações são transmitidas nas propriedades da mensagem pelo aplicativo, em vez de no corpo da mensagem, para que o processador de evento não precise desserializar a mensagem apenas para executar o roteamento.
   
   > [!NOTE]
   > É importante criar o **MessageId** usado para eliminar a duplicação de mensagens interativas no código do dispositivo. As comunicações de rede intermitente, ou outras falhas, podem resultar em várias retransmissões da mesma mensagem deste dispositivo. Você também pode usar uma ID de mensagem semântica, como um hash dos campos de dados da mensagem relevante, no lugar de um UUID.
   > 
   > 
3. Modifique o método **main** para enviar mensagens interativas e de ponto de dados, conforme mostrado no trecho de código a seguir:
   
    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();
   
    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````
4. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.
   
   > [!NOTE]
   > Para simplificar, esse tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como uma retirada exponencial, como sugerido no artigo [Tratamento de falhas transitórias]do MSDN.
   > 
   > 
5. Para compilar o aplicativo **simulated-device** usando o Maven, execute o comando a seguir no prompt de comando na pasta simulated-device:
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="process-devicetocloud-messages"></a>Processar mensagens de dispositivo para nuvem
Nesta seção, você criará um aplicativo do console do Java que processa mensagens do dispositivo para a nuvem do Hub IoT. Um Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos]para permitir que um aplicativo leia mensagens do dispositivo para a nuvem. Este tutorial usa a classe [EventProcessorHost] para processar essas mensagens em um aplicativo de console. Para obter mais informações sobre como processar as mensagens dos Hubs de Eventos, confira o tutorial [Introdução aos Hubs de Eventos] .

Quando você implementa o armazenamento confiável de mensagens de ponto de dados ou encaminha mensagens interativas, o principal desafio é que o processamento do evento depende do consumidor da mensagem para fornecer os pontos de verificação para seu progresso. Além disso, para alcançar uma alta taxa de transferência ao ler dos Hubs de Eventos, é necessário fornecer um ponto de verificação em lotes grandes. Isso criará a possibilidade de processamento duplicado para um grande número de mensagens, se houver uma falha e se você reverter para o ponto de verificação anterior. Neste tutorial, você verá como sincronizar as gravações de armazenamento do Azure e as janelas de eliminação de duplicação do Barramento de Serviço com os pontos de verificação do **EventProcessorHost** .

Para gravar mensagens no armazenamento do Azure de forma confiável, o exemplo usa o recurso [blobs de blocos][Blobs de bloco do Azure] de confirmação de bloco individual. O processador de eventos acumula mensagens na memória até o momento de fornecer um ponto de verificação. Por exemplo, depois que o buffer acumulado de mensagens atingir o tamanho máximo de bloco de 4 MB, ou depois que a janela de tempo de duplicação do Barramento de Serviço expirar. Então, antes do ponto de verificação, o código confirma novo bloco no blob.

O processador de evento usa o deslocamento de mensagem dos Hubs de Eventos como IDs de bloco. Este mecanismo permite que o processador de eventos execute uma verificação de eliminação de duplicação antes de confirmar o novo bloco de armazenamento, tomando cuidado com uma possível falha entre a confirmação de um bloco e o ponto de verificação.

> [!NOTE]
> Este tutorial usa uma única conta de armazenamento para gravar todas as mensagens recuperadas do Hub IoT. Para decidir se você precisará usar várias contas de armazenamento do Azure em sua solução, confira [Azure Storage scalability Guidelines](Diretrizes de escalabilidade do Armazenamento do Azure.md).
> 
> 

O aplicativo usa o recurso de eliminação de duplicação do Barramento de Serviço para evitar duplicatas quando processa mensagens interativas. O dispositivo simulado carimba cada mensagem interativa com uma única **MessageId**. Esta ID permite que o Barramento de Serviço possa garantir que, na janela de tempo de eliminação de duplicação especificada, duas mensagens com a mesma **MessageId** não sejam entregues aos destinatários. Essa eliminação de duplicação, junto com a semântica de conclusão por mensagem fornecida pelas filas do Barramento de Serviço, facilita o processamento confiável de mensagens interativas.

Para garantir que nenhuma mensagem seja reenviada fora da janela de eliminação de duplicação, o código sincroniza o mecanismo de ponto de verificação **EventProcessorHost** com a janela de eliminação de duplicação de fila do Barramento de Serviço. Esta sincronização é realizada pela imposição de um ponto de verificação pelo menos uma vez sempre a janela de eliminação de duplicação expirar (neste tutorial, a janela é de uma hora).

> [!NOTE]
> Este tutorial usa uma única fila de Barramento de Serviço particionada para processar todas as mensagens interativas recuperadas do Hub IoT. Para obter mais informações sobre como usar filas do Barramento de Serviço para atender aos requisitos de escalabilidade da sua solução, confira a documentação [Barramento de Serviço do Azure] .
> 
> 

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço
Para usar a classe [EventProcessorHost] , você deve ter uma conta de armazenamento do Azure para habilitar o **EventProcessorHost** para registrar as informações do ponto de verificação. Você pode usar uma conta de armazenamento existente ou seguir as instruções em [Sobre o Armazenamento do Azure] para criar uma nova. Anote a cadeia de conexão da conta de armazenamento.

> [!NOTE]
> Ao copiar e colar a cadeia de conexão da conta de armazenamento, verifique se não há nenhum espaço incluído.
> 
> 

Você também precisará de uma fila do Barramento de Serviço para habilitar o processamento confiável de mensagens interativas. Você pode criar uma fila programaticamente com uma janela de eliminação de duplicação de uma hora, como explicado em [Como usar filas do Barramento de Serviço][Fila do Barramento de Serviço]. Como alternativa, você pode usar o [portal clássico do Azurel][lnk-classic-portal] seguindo estas etapas:

1. Clique em **Novo** no canto inferior esquerdo. Em seguida, clique em **Serviços de aplicativos** > **Barramento de Serviço** > **Fila** > **Criação Personalizada**. Insira o nome **d2ctutorial**, selecione uma região e use um namespace existente ou crie um novo. Anote o nome do namespace. Você precisará dele mais tarde neste tutorial. Na página seguinte, selecione **Habilitar detecção de duplicidades** e defina a **Janela de tempo do histórico de detecção de duplicidades** como uma hora. Em seguida, clique na marca de seleção no canto inferior direito para salvar a configuração de fila.
   
    ![Criar uma fila usando o portal do Azure][30]
2. Na lista de filas do Barramento de Serviço, clique em **d2ctutorial** e, em seguida, clique em **Configurar**. Crie duas políticas de acesso compartilhado, uma chamada **enviar** com permissões de **Envio** e outra chamada **escutar** com permissões de **Escuta**. Anote os valores da **chave primária** para ambas as políticas. Você precisará delas mais tarde neste tutorial. Quando terminar, clique em **Salvar** na parte inferior.
   
    ![Configurar uma fila no portal do Azure][31]

### <a name="create-the-event-processor"></a>Criar o processador de eventos
Nesta seção, você deve criar um aplicativo Java para processar mensagens do ponto de extremidade compatível com os Hubs de Eventos.

A primeira tarefa é adicionar um projeto Maven chamado **process-d2c-messages** , que recebe mensagens do dispositivo para a nuvem do ponto de extremidade compatível com os Hubs de Eventos do Hub IoT e encaminha essas mensagens para outros serviços de back-end.

1. Na pasta iot-java-get-started criada no tutorial [Introdução ao Hub IoT] , crie um projeto Maven denominado **process-d2c-messages** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. No prompt de comando, navegue até a nova pasta process-d2c-messages.
3. Usando um editor de texto, abra o arquivo pom.xml na pasta process-d2c-messages e adicione as dependências a seguir ao nó **dependencies** . Essas dependências permitem que você use os pacotes azure-eventhubs, azure-eventhubs-eph e azure-servicebus em seu aplicativo para interagir com o Hub IoT e com a fila do Barramento de Serviço:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```
4. Salve e feche o arquivo pom.xml.

A próxima tarefa é adicionar uma classe **ErrorNotificationHandler** ao projeto.

1. Use um editor de texto para criar um arquivo process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java. Adicione o seguinte código ao arquivo para exibir as mensagens de erro da instância **EventProcesssorHost** :
   
    ```
    package com.mycompany.app;
   
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```
2. Salve e feche o arquivo ErrorNotificationHandler.java.

Agora, você pode adicionar uma classe que implementa a interface **IEventProcessor** . A classe **EventProcessorHost** chama essa classe para processar mensagens de dispositivo para nuvem recebidas do Hub IoT. O código nessa classe implementa a lógica para armazenar mensagens de forma confiável em um contêiner de blob e encaminhar mensagens interativas para a fila do Barramento de Serviço.

O método **onEvents** define a variável **latestEventData** que controla o deslocamento e o número de sequência da mensagem lida mais recentemente por este processador de eventos. Lembre-se de que cada processador é responsável por uma única partição. O método **onEvents** recebe um lote de mensagens do Hub IoT e o processa da seguinte maneira: ele envia mensagens interativas para a fila do Barramento de Serviço e anexa as mensagens de ponto de dados no buffer de memória **toAppend**. Se o buffer de memória atingir o limite de bloqueio de 4 MB ou se as janelas de tempo de eliminação de duplicação do Barramento de Serviço expirarem (uma hora após o último ponto de verificação neste tutorial), um ponto de verificação será disparado pelo método.

O método **AppendAndCheckPoint** primeiro gera uma **blockId** para o bloco a ser anexado no blob. O Armazenamento do Azure exige que todas as IDs de bloco tenham o mesmo comprimento, então o método preenche o deslocamento com zeros à esquerda. Em seguida, se um bloco com essa ID já estiver no blob, o método o substituirá pelo conteúdo de buffer atual.

> [!NOTE]
> Para simplificar o código, este tutorial usa um arquivo único de blob por partição para armazenar as mensagens. Uma solução real implementaria o arquivo sem interrupção, criando arquivos adicionais após um determinado período, ou quando um tamanho estipulado fosse atingido. Lembre-se de que um blob de blocos do Azure pode conter, no máximo, 195 GB de dados.
> 
> 

A próxima tarefa é implementar a interface **IEventProcessor** :

1. Use um editor de texto para criar um arquivo process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java.
2. Adicione as seguintes importações e definição de classe no arquivo EventProcessor.java. A classe **EventProcessor** implementa a interface **IEventProcessor**, que define o comportamento do cliente dos Hubs de Eventos:
   
    ```
    package com.mycompany.app;
   
    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;
   
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;
   
    public class EventProcessor implements IEventProcessor {
   
    }
    ```
3. Adicione os seguintes métodos à classe **EventProcessor** para implementar a interface **IEventProcessor**:
   
    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }
   
    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }
   
    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }
   
    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```
4. Adicione as seguintes variáveis no nível da classe à classe **EventProcessor** :
   
    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;
   
    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);
   
    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```
5. Adicione um método **AppendAndCheckPoint** com a assinatura a seguir na classe **EventProcessor**:
   
    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```
6. Adicione o seguinte código no método **AppendAndCheckPoint** para recuperar o deslocamento e a sequência de número da mensagem atual na partição:
   
    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```
7. No método **AppendAndCheckPoint**, use o valor de deslocamento atual para criar uma instância **BlockEntry** para o próximo bloco salvar o blob:
   
    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```
8. No método **AppendAndCheckPoint** , carregue o conjunto mais recente de mensagens no blob de blocos e recupere a lista atual de blocos:
   
    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);
   
    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```
9. No método **AppendAndCheckPoint** , crie o bloqueio inicial em um blob novo ou acrescente o bloco no blob existente:
   
    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```
10. Por fim, no método **AppendAndCheckPoint** , crie um ponto de verificação na partição e prepare para salvar o próximo bloco de mensagens:
    
    ```
    context.checkpoint(latestEventData);
    
    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```
11. No método **onEvents** , adicione o seguinte código para receber mensagens do ponto de extremidade do Hub IoT e encaminhar mensagens interativas para a fila do Barramento de Serviço. Em seguida, chame o método **AppendAndCheckPoint** quando o bloco estiver cheio ou o tempo limite expirar:
    
    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```
12. Por fim, no método**onEvents**, adicione uma cláusula 'else if' para chamar o **AppendAndCheckPoint** se o tempo limite expirar enquanto não houver mensagens provenientes do Hub IoT:
    
    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```
13. Salve as alterações no arquivo EventProcessor.java.

A tarefa final no projeto**process-d2c-messages** é adicionar o código no método **main** que cria uma instância **EventProcessorHost**.

1. Use um editor de texto para abrir o arquivo process-d2c-messages\src\main\java\com\mycompany\app\App.java.
2. Adicione as seguintes instruções **import** ao arquivo:
   
    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;
   
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```
3. Adicione as seguintes variáveis em nível de classe à classe **App** . Substitua **{yourstorageaccountconnectionstring}** pela cadeia de conexão da conta de armazenamento do Azure que você anotou anteriormente na seção [Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :
   
    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```
4. Adicione as seguintes variáveis de nível de classe na classe **App** e substitua **{yourservicebusnamespace}** pelo namespace do Barramento de Serviço e **{yourservicebussendkey}** pela chave **send** da fila. Você anotou um namespace anteriormente e **escutou** os valores principais na seção [Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :
   
    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```
5. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua **{youreventhubcompatibleendpoint}** pelo nome do ponto de extremidade do Hub de Eventos compatível. O nome do ponto de extremidade se parece com **ihs....namespace**. Portanto, você deve remover o prefixo **sb://** e o sufixo **.servicebus.windows.net/**. Substitua **{youreventhubcompatiblename}** pelo nome do hub de eventos compatível. Substitua **{youriothubkey}** pela chave **iothubowner**. Você anotou esses valores na seção [Criar um IoT Hub][lnk-create-an-iot-hub] e no tutorial *Introdução ao Hub IoT do Azure para Java*:
   
    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```
6. Modifique a assinatura do método **main** da seguinte maneira:
   
    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```
7. Adicione o seguinte código no método **main** para obter uma referência para o contêiner de blob que armazena as mensagens:
   
    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```
8. Adicione o seguinte código ao método **main** para obter uma referência para o serviço de Barramento de Serviço:
   
    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```
9. No método **main**, configure e crie uma instância **EventProcessorHost**. A opção **setInvokeProcessorAfterReceiveTimeout** garante que a instância **EventProcessorHost** invocará o método **onEvents** na interface **IEventProcessor**, mesmo quando não houver nenhuma mensagem para processar. O método **onEvents** sempre chamará o método **AppendAndCheckPoint** quando o tempo limite expirar.
   
    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```
10. No método **main**, registra a implementação **IEventProcessor** com a instância **EventProcessorHost**:
    
    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```
11. Finalmente, adicione lógica ao método **main** para desligar a instância **EventProcessorHost**:
    
    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();
    
      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }
    
    System.out.println("End of sample");
    ```
12. Salve e feche o arquivo process-d2c-messages\src\main\java\com\mycompany\app\App.java.
13. Para compilar o aplicativo **process-d2c-messages** usando o Maven, execute o comando a seguir no prompt de comando, na pasta process-d2c-messages:
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Receber mensagens interativas
Nesta seção, você gravará um aplicativo de console do Java que recebe mensagens interativas da fila do Barramento de Serviço.

A primeira tarefa é adicionar um projeto Maven chamado **process-interactive-messages**, que recebe as mensagens enviadas na fila do Barramento de Serviço das instâncias **EventProcessor**.

1. Na pasta iot-java-get-started criada no tutorial [Introdução ao Hub IoT] , crie um projeto Maven denominado **process-interactive-messages** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. No prompt de comando, navegue até a nova pasta process-interactive-messages.
3. Usando um editor de texto, abra o arquivo pom.xml na pasta process-interactive-messages e adicione a dependência a seguir ao nó **dependencies** . Essa dependência permite que você use o pacote do Barramento de Serviço do Azure em seu aplicativo para interagir com a fila do Barramento de Serviço:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```
4. Salve e feche o arquivo pom.xml.

A próxima tarefa é adicionar o código para recuperar mensagens da fila de Barramento de Serviço.

1. Use um editor de texto para abrir o arquivo process-interactive-messages\src\main\java\com\mycompany\app\App.java.
2. Adicione as instruções `import` a seguir ao arquivo:
   
    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
   
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```
3. Adicione as seguintes variáveis de nível de classe na classe **App** e substitua **{yourservicebusnamespace}** pelo namespace do Barramento de Serviço e**{yourservicebuslistenkey}** pela chave **listen** da fila. Você anotou um namespace anteriormente e **escutou** os valores principais na seção [Provisionar uma conta de Armazenamento do Azure e uma fila do Barramento de Serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :
   
    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```
4. Adicione a seguinte classe aninhada na classe **App** para receber mensagens da fila:
   
    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```
5. Modifique a assinatura do método **main** da seguinte maneira:
   
    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```
6. No método **main** , adicione o seguinte código para iniciar a escuta para as novas mensagens:
   
    ```
    System.out.println("Process interactive messages");
   
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);
   
    MessageReceiver receiver = new MessageReceiver();
   
    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);
   
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```
7. Salve e feche o arquivo process-interactive-messages\src\main\java\com\mycompany\app\App.java.
8. Para compilar o aplicativo **process-interactive-messages** usando o Maven, execute o comando a seguir no prompt de comando na pasta process-interactive-messages:
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar os aplicativos
Agora, você está pronto para executar os três aplicativos.

1. Para executar o aplicativo **process-interactive-messages** , em um prompt de comando ou shell, navegue até a pasta process-interactive-messages e execute o seguinte comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar process-interactive-messages][processinteractive]
2. Para executar o aplicativo **process-d2c-messages** , em um prompt de comando ou shell, navegue até a pasta process-d2c-messages e execute o seguinte comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar process-d2c-messages][processd2c]
3. Para executar o aplicativo **simulated-device** , em um prompt de comando ou shell, navegue até a pasta simulated-device e execute o seguinte comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar simulated-device][simulateddevice]

> [!NOTE]
> Para ver as atualizações em seu arquivo de blob, talvez seja necessário reduzir a constante **MAX_BLOCK_SIZE** na classe **StoreEventProcessor** para um valor menor, como **1024**. Esta alteração é útil porque leva algum tempo para alcançar o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho de bloco menor, você não precisa esperar muito tempo para ver o blob ser criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais dimensionável.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a processar com segurança as mensagens de ponto de dados e interativas do dispositivo para a nuvem usando a classe [EventProcessorHost] .

O tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d] mostra como enviar mensagens para seus dispositivos de back-end.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Armazenamento de Blobs do Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Fila do Barramento de Serviço]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guia do desenvolvedor do Hub IoT do Azure — dispositivo para nuvem]: iot-hub-devguide-messaging.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao Hub IoT]: iot-hub-java-java-getstarted.md
[Centro de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Sobre o Armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introdução aos Hubs de Eventos]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Blobs de bloco do Azure]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Hubs de Eventos]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub


<!--HONumber=Oct16_HO2-->


