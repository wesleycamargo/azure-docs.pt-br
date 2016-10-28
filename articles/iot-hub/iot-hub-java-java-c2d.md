<properties
	pageTitle="Enviar mensagens da nuvem para o dispositivo com o Hub IoT| Microsoft Azure"
	description="Siga este tutorial para aprender a enviar mensagens da nuvem para o dispositivo usando o Hub IoT do Azure com Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="dobett"/>

# Tutorial: Como enviar mensagens da nuvem para o dispositivo com o Hub IoT e Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. O tutorial [Introdução ao Hub IoT] mostra como criar um hub IoT, provisionar uma identidade do dispositivo e codificar um dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Esse tutorial se baseia na [Introdução ao Hub IoT]. Ele mostra como:

- Do seu aplicativo de nuvem de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.
- Receber mensagens da nuvem para o dispositivo em um dispositivo.
- Do seu aplicativo de nuvem de back-end, solicitar confirmação de entrega (*comentários*) para as mensagens enviadas a um dispositivo do Hub IoT.

Encontre mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

No final deste tutorial, você executará dois aplicativos de console do Java:

* **simulated-device**, uma versão modificada do aplicativo criado na [Introdução ao Hub IoT], que se conecta a seu Hub IoT e recebe mensagens da nuvem para o dispositivo.
* **send-c2d-messages**, que envia uma mensagem da nuvem ao dispositivo simulado por meio do Hub IoT e recebe sua confirmação de entrega.

> [AZURE.NOTE] O Hub IoT tem suporte a SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) nos SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure].

Para concluir este tutorial, você precisará do seguinte:

+ Java SE 8. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.

+ Maven 3. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Maven para este tutorial no Windows ou no Linux.

+ Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

## Receber mensagens no dispositivo simulado

Nesta seção, você modificará o aplicativo do dispositivo simulado criado na [Introdução ao Hub IoT] para receber mensagens da nuvem para o dispositivo do hub IoT.

1. Usando um editor de texto, abra o arquivo simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Adicione a classe **MessageCallback** a seguir como uma classe aninhada dentro da class **App**. O método **execute** é invocado quando o dispositivo recebe uma mensagem do Hub IoT. Neste exemplo, o dispositivo sempre notifica o hub que concluiu a mensagem:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifique o método **main** para criar uma instância **MessageCallback** e chame o método **setMessageCallback** antes de abrir o cliente da seguinte maneira:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Se você usar HTTP/1 em vez de AMQP como transporte, a instância **DeviceClient** verificará se há mensagens do Hub IoT com pouca frequência (menos de cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte do AMQP e HTTP/1 e a limitação do Hub IoT, confira o [Guia do desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

## Envie uma mensagem da nuvem para o dispositivo

Nesta seção, você criará um aplicativo do console do Java que envia mensagens da nuvem para o dispositivo para o aplicativo do dispositivo simulado. Você precisa da Id do dispositivo que você adicionou no tutorial [Introdução ao Hub IoT]. Você também precisa da cadeia de conexão do Hub IoT, que você pode encontrar no [Portal do Azure].

1. Crie um projeto Maven chamado **send-c2d-messages** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a nova pasta send-c2d-messages.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta send-c2d-messages e adicione a dependência a seguir ao nó **dependencies**. A adição da dependência permite que você use o pacote **iothub-java-service-client** no aplicativo para se comunicar com o serviço do Hub IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis do nível da classe à classe **App**, substituindo **{yourhubconnectionstring}** e **{yourdeviceid}** pelos valores anotados anteriormente:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
    
8. Substitua o método **main** pelo código a seguir que se conecta ao Hub IoT, envia uma mensagem ao dispositivo e aguarda uma confirmação de que o dispositivo recebeu e processou a mensagem:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugestão no artigo [Tratamento de Falhas Transitórias] do MSDN.

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta do dispositivo simulado, execute o seguinte comando para iniciar o envio de dados de telemetria para seu Hub IoT e escutar mensagens da nuvem para o dispositivo enviadas do hub:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Executar um aplicativo de dispositivo simulado][img-simulated-device]

2. Em um prompt de comando na pasta send-c2d-messages, execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e esperar uma confirmação de comentário:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o comando para enviar a mensagem da nuvem para o dispositivo][img-send-command]

## Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]: media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Introdução ao Hub IoT]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0914_2016-->