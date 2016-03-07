## Criar uma identidade do dispositivo

Nesta seção, você criará um aplicativo do console do Java que cria uma nova identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Veja a seção **Registro de identidade do dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e uma chave de dispositivo exclusivas com que seu dispositivo poderá se identificar ao enviar mensagens de dispositivo para a nuvem para o Hub IoT.

1. Crie uma nova pasta vazia chamada iot-java-get-started. Na pasta iot-java-get-started, crie um novo projeto Maven chamado **create-device-identity** usando o comando a seguir no prompt de comando. Observe que é um comando único e longo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a nova pasta create-device-identity.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta create-device-identity e adicione a dependência a seguir ao nó **dependencies**. Isso permite que você use o pacote iothub-service-sdk no aplicativo:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    ```
    
4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **App**, substituindo **{yourhubname}** e **{yourhubkey}** pelos valores anotados anteriormente:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Modifique a assinatura do método **main** para incluir as exceções mostradas abaixo:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Adicione o código a seguir ao corpo do método **main**. Esse código cria um dispositivo chamado *javadevice* no registro de identidade do Hub IoT caso ele ainda não exista. Ele então exibe a id e a chave do dispositivo que serão necessárias posteriormente:

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

11. Para criar o aplicativo **create-device-identity** usando o Maven, execute o comando a seguir no prompt de comando na pasta create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Para executar o aplicativo **create-device-identity** usando o Maven, execute o comando a seguir no prompt de comando na pasta create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Anote a **Id do dispositivo** e a **Chave do dispositivo**. Você precisará delas mais tarde quando criar um aplicativo que se conecte ao Hub IoT como um dispositivo.

> [AZURE.NOTE] O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves de dispositivo para usar como credenciais de segurança e um sinalizador habilitado/desabilitado que permite que você desabilite o acesso para um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Veja o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para saber mais.

## Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo do console do Java que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-processd2c-tutorial] mostra como processar mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais sobre como processar mensagens de Hubs de Eventos e se aplica aos pontos de extremidade compatíveis com o Hub de Eventos Hub IoT.

1. Na pasta iot-java-get-started, criada na seção *Criar uma identidade de dispositivo*, crie um novo projeto Maven chamado **read-d2c-messages** usando o comando a seguir no prompt de comando. Observe que é um comando único e longo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a nova pasta read-d2c-messages.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta read-d2c-messages e adicione a dependência a seguir ao nó **dependencies**. Isso permite usar o pacote eventhubs-client em seu aplicativo para ler a partir do ponto de extremidade compatível com Hubs de Eventos:

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **App**:

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Adicione a classe aninhada a seguir à classe **App**. O aplicativo cria dois threads para executar o **MessageReceiver** para ler mensagens de duas partições no Hub de Eventos:

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Adicione o construtor a seguir à classe **MessageReceiver**:

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Adicione o método **run** a seguir à classe **MessageReceiver**. Esse método cria uma instância de **EventHubReceiver** para ler de uma partição de Hub de Eventos. Ele repetirá continuamente um loop e exibirá detalhes da mensagem no console até que **stopThread** seja verdadeiro.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Esse método usa um filtro ao criar o receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Isso será útil em um ambiente de teste para que você possa ver o conjunto de mensagens atual, mas em um ambiente de produção, seu código deverá garantir o processamento de todas as mensagens - veja o tutorial [Como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-processd2c-tutorial] para saber mais.

11. Modifique a assinatura do método **main** para incluir as exceções mostradas abaixo:

    ```
    public static void main( String[] args ) throws IOException
    ```

12. Adicione o método a seguir ao método **main** da classe **App**. Esse código cria uma instância de **EventHubClient** para se conectar ao ponto de extremidade compatível com o Hub de Eventos no hub IoT. Em seguida, ele cria dois threads para ler as duas partições. Substitua **{youriothubkey}**, **{youreventhubcompatiblenamespace}** e **{youreventhubcompatiblename}** pelos valores anotados anteriormente. O valor do espaço reservado **{youreventhubcompatiblenamespace}** é proveniente do **ponto de extremidade compatível com o Hub de Eventos**: ele tem o formato **xxxxnamespace** (em outras palavras, remova o prefixo ****sb://** e o sufixo **.servicebus.windows.net** do valor do ponto de extremidade compatível com o Hub de Eventos do portal).

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] Esse código pressupõe que você tenha criado seu hub IoT na camada F1 (gratuita). Um hub IoT gratuito tem duas partições chamadas "0" e "1". Se você tiver criado o hub IoT usando um dos outros tipos de preços, deverá ajustar o código para criar um **MessageReceiver** para cada partição.

13. Salve e feche o arquivo App.java.

14. Para compilar o aplicativo **read-d2c-messages** usando o Maven, execute o comando a seguir no prompt de comando na pasta read-d2c-messages:

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0224_2016-->