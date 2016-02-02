## Criar um aplicativo de dispositivo simulado

Nesta seção, você criará um aplicativo do console do Java que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. Na pasta iot-java-get-started, criada na seção *Criar uma identidade de dispositivo*, crie um novo projeto Maven chamado **simulated-device** usando o comando a seguir no prompt de comando. Observe que é um comando único e longo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a nova pasta simulated-device.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta simulated-device e adicione as seguintes dependências ao nó **dependências**. Isso permite que você use o pacote iothub-java-client em seu aplicativo para se comunicar com o hub IoT e para serializar objetos Java para JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.0-preview.9</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Adicione as seguintes variáveis de nível de classe à classe **App**, substituindo **{youriothubname}** pelo nome do hub Iot e **{yourdeviceid}** e **{yourdevicekey}** pelos valores do dispositivo gerados na seção *Criar uma identidade de dispositivo*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Este aplicativo de exemplo usa a variável **protocol** quando instancia um objeto **DeviceClient**. Você pode usar o protocolo HTTPS ou AMQPS para se comunicar com o Hub IoT.

8. Adicione a classe aninhada **TelemetryDataPoint** a seguir à classe **App** para especificar os dados de telemetria enviados pelo dispositivo para o hub IoT:

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

9. Adicione a classe aninhada **EventCallback** a seguir à classe **App** para exibir o status de confirmação que o hub IoT retorna quando processa uma mensagem do dispositivo simulado. Esse método também notifica o thread principal do aplicativo quando a mensagem tiver sido processada:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Adicione a classe aninhada **MessageSender** a seguir à classe **App**. O método **run** nesta classe gera dados de telemetria de exemplo a serem enviados para o hub IoT e aguarda uma confirmação antes de enviar a mensagem seguinte:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Esse método envia uma nova mensagem de dispositivo para a nuvem um segundo depois que o hub IoT confirma a mensagem anterior. A mensagem contém um objeto serializado por JSON com a deviceId e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

11. Substitua o método **main** pelo código a seguir, que cria um thread para enviar mensagens do dispositivo para a nuvem para o hub IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Salve e feche o arquivo App.java.

13. Para criar o aplicativo **simulated-device** usando o Maven, execute o comando a seguir no prompt de comando na pasta simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/pt-BR/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0128_2016-->