---
title: Introdução ao Hub IoT para Java | Microsoft Docs
description: Tutorial de introdução ao Hub IoT do Azure para Java. Use o Hub IoT do Azure e Java com SDKs de IoT do Microsoft Azure para implementar uma solução de Internet das Coisas.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: dobett

---
# <a name="get-started-with-azure-iot-hub-for-java"></a>Introdução ao Hub IoT do Azure para Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Ao fim deste tutorial, você tem três aplicativos de console do Java:

* **create-device-identity**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **read-d2c-messages**, que exibe a telemetria enviada pelo dispositivo simulado.
* **simulated-device**, que conecta o Hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo AMQPS.

> [!NOTE]
> O artigo [SDKs do Hub IoT][lnk-hub-sdks]fornece informações sobre vários SDKs que você pode usar para criar aplicativos para executar nos dispositivos e no back-end da solução.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Java SE 8. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.
* Maven 3.  <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Maven para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como etapa final, anote o valor da **Chave primária**, em seguida, clique em **Mensagens**. Na folha **Mensagens**, anote o **nome compatível com o Hub de Eventos** e o **ponto de extremidade compatível com o Hub de Eventos**. Você precisa desses três valores quando cria o aplicativo **read-d2c-messages** .

![Folha Mensagens do Hub IoT do Portal do Azure][6]

Você criou seu hub IoT e tem o nome de host do Hub IoT, a cadeia de conexão do Hub IoT, a Chave Primária do Hub IoT, o nome compatível com o Hubs de Eventos e o ponto de extremidade compatível com o Hubs de Eventos necessários para concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo
Nesta seção, você cria um aplicativo do console do Java que cria uma nova identidade do dispositivo no registro de identidade em seu Hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Consulte a seção **Registro de Identidade do Dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e chave do dispositivo exclusivas com as quais seu dispositivo poderá identificar-se ao enviar mensagens entre o dispositivo e a nuvem para o Hub IoT.

1. Crie uma nova pasta vazia chamada iot-java-get-started. Na pasta iot-java-get-started, crie um novo projeto Maven denominado **create-device-identity** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. No prompt de comando, navegue até a nova pasta create-device-identity.
3. Usando um editor de texto, abra o arquivo pom.xml na pasta create-device-identity e adicione a dependência a seguir ao nó **dependencies** . Isso permite que você use o pacote iothub-service-sdk no aplicativo:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
4. Salve e feche o arquivo pom.xml.
5. Usando um editor de texto, abra o arquivo create-device-identity\src\main\java\com\mycompany\app\App.java.
6. Adicione as seguintes instruções **import** ao arquivo:
   
    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Adicione as seguintes variáveis no nível da classe à classe **App**, substituindo **{cadeiadeconexãodoseuhub}** pelo valor anotado anteriormente:
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Modifique a assinatura do método **main** para incluir as exceções abaixo:
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Adicione o código a seguir como o corpo do método **main** . Esse código cria um dispositivo denominado *javadevice* no registro de identidade do Hub IoT, caso ele ainda não exista. Ele exibe a ID e a chave do dispositivo que são necessárias posteriormente:
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Salve e feche o arquivo App.java.
11. Para compilar o aplicativo **create-device-identity** usando o Maven, execute o comando a seguir no prompt de comando na pasta create-device-identity:
    
    ```
    mvn clean package -DskipTests
    ```
12. Para executar o aplicativo **create-device-identity** usando o Maven, execute o comando a seguir no prompt de comando na pasta create-device-identity:
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Anote a **Id do dispositivo** e a **Chave do dispositivo**. Você precisará delas mais tarde quando criar um aplicativo que se conecte ao Hub IoT como um dispositivo.

> [!NOTE]
> O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Consulte o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações.
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens do dispositivo para a nuvem
Nesta seção, você cria um aplicativo do console do Java que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com o [Hub de Eventos][lnk-event-hubs-overview]para que você possa ler as mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra como processar as mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] fornece informações adicionais sobre como processar as mensagens dos Hubs de Eventos e é aplicável aos pontos de extremidade compatíveis com os Hubs de Eventos do Hub IoT.

> [!NOTE]
> O ponto de extremidade compatível com os Hubs de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQPS.
> 
> 

1. Na pasta iot-java-get-started criada na seção *Criar uma identidade de dispositivo* , crie um novo projeto Maven denominado **read-d2c-messages** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. No prompt de comando, navegue até a nova pasta read-d2c-messages.
3. Usando um editor de texto, abra o arquivo pom.xml na pasta read-d2c-messages e adicione a dependência a seguir ao nó **dependencies** . Isso permite usar o pacote eventhubs-client em seu aplicativo para ler a partir do ponto de extremidade compatível com Hubs de Eventos:
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```
4. Salve e feche o arquivo pom.xml.
5. Usando um editor de texto, abra o arquivo read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Adicione as seguintes instruções **import** ao arquivo:
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
   
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```
7. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua **{suachavehubiot}**, **{pontoextremidadecompatívelcomseuhubdeeventos}** e **{nomecompatívelcomseuhubdeeventos}** pelos valores que você anotou anteriormente:
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Adicione o seguinte método **receiveMessages** à classe **App**. Esse método cria uma instância **EventHubClient** para conectar o ponto de extremidade compatível com os Hubs de Eventos e cria assincronamente uma instância **PartitionReceiver**para ler em uma partição do Hub de Eventos. Ele faz um loop contínuo e imprime os detalhes da mensagem até o aplicativo ser encerrado.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Esse método usa um filtro ao criar o receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Isso é útil em um ambiente de teste para que você possa ver o conjunto atual de mensagens. Em um ambiente de produção, seu código deve verificar se ele processa todas as mensagens - consulte o tutorial [Como processar as mensagens do dispositivo para a nuvem do Hub IoT][lnk-process-d2c-tutorial] para obter mais informações.
   > 
   > 
9. Modifique a assinatura do método **main** para incluir a exceção mostrada abaixo:
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Adicione o seguinte código ao método **main** na classe **App**. Esse código cria as duas instâncias **EventHubClient** e **PartitionReceiver**, e permite que você feche o aplicativo quando terminar de processar as mensagens:
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > Esse código pressupõe que você tenha criado seu hub IoT na camada F1 (gratuita). Um hub IoT gratuito tem duas partições chamadas "0" e "1".
    > 
    > 
11. Salve e feche o arquivo App.java.
12. Para compilar o aplicativo **read-d2c-messages** usando o Maven, execute o comando a seguir no prompt de comando na pasta read-d2c-messages:
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo do console do Java que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um Hub IoT.

1. Na pasta iot-java-get-started, criada na seção *Criar uma identidade do dispositivo* , crie um novo projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. No prompt de comando, navegue até a nova pasta simulated-device.
3. Usando um editor de texto, abra o arquivo pom.xml na pasta simulated-device e adicione as seguintes dependências ao nó **dependencies** . Isso permite que você use o pacote iothub-java-client em seu aplicativo para se comunicar com o hub IoT e para serializar objetos Java para JSON:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Salve e feche o arquivo pom.xml.
5. Usando um editor de texto, abra o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.
6. Adicione as seguintes instruções **import** ao arquivo:
   
    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Adicione as seguintes variáveis no nível da classe à classe **App**, substituindo **{nomedoseuhubiot}** por seu nome do hub IoT e **{chavedoseudispositivo}** pelo valor da chave do dispositivo gerado na seção *Criar uma identidade do dispositivo*:
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Este aplicativo de exemplo usa a variável **protocol** quando cria uma instância de um objeto **DeviceClient**. Você pode usar o protocolo HTTPS ou AMQPS para se comunicar com o Hub IoT.
8. Adicione a seguinte classe **TelemetryDataPoint** aninhada dentro da classe **App** para especificar os dados de telemetria que seu dispositivo envia para o hub IoT:
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Adicione a seguinte classe **EventCallback** aninhada dentro da classe **App** para exibir o status de confirmação que o hub IoT retorna ao processar uma mensagem a partir do dispositivo simulado. Esse método também notifica o thread principal do aplicativo quando a mensagem tiver sido processada:
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. Adicione a seguinte classe **MessageSender** aninhada dentro da classe **App**. O método **run** nessa classe gera dados de telemetria de exemplo a serem enviados para o hub IoT e aguarda uma confirmação antes de enviar a próxima mensagem:
    
    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```
    
    Esse método envia uma nova mensagem de dispositivo para a nuvem um segundo depois que o hub IoT confirma a mensagem anterior. A mensagem contém um objeto serializado por JSON com a deviceId e um número gerado aleatoriamente para simular um sensor de velocidade do vento.
11. Substitua o método **main** pelo código a seguir, que cria um thread para enviar mensagens do dispositivo para a nuvem para o hub IoT:
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Salve e feche o arquivo App.java.
13. Para compilar o aplicativo **simulated-device** usando o Maven, execute o comando a seguir no prompt de comando na pasta simulated-device:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como retirada exponencial), conforme sugerido no artigo do MSDN [Tratamento de Falhas Transitórias][lnk-transient-faults].
> 
> 

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta read-d2c, execute o seguinte comando para iniciar o monitoramento da primeira partição do seu hub IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Aplicativo de cliente do serviço do Hub IoT do Java para monitorar mensagens do dispositivo para a nuvem][7]
2. Em um prompt de comando na pasta simulated-device, execute o seguinte comando para iniciar o envio de dados de telemetria para seu hub IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Aplicativo de cliente do dispositivo do Hub IoT do Java para enviar mensagens do dispositivo para a nuvem][8]
3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o hub:
   
    ![Bloco de Uso do Portal do Azure mostrando o número de mensagens enviadas ao Hub IoT][43]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade do dispositivo no registro de identidade do hub. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para enviar as mensagens entre o dispositivo e a nuvem para o hub. Você também criou um aplicativo que exibe as mensagens recebidas pelo hub. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Conectar o dispositivo][lnk-connect-device]
* [Introdução ao gerenciamento de dispositivos][lnk-device-management]
* [Introdução ao SDK do Gateway][lnk-gateway-SDK]

Para saber como estender sua solução de IoT e processar as mensagens do dispositivo para a nuvem em escala, consulte o tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial].

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Oct16_HO2-->


