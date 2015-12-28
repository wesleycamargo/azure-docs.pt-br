<properties
	pageTitle="Criar um cliente Java do Hub IoT | Microsoft Azure"
	description="Siga este tutorial para conseguir compilar um cliente Java do Hub IoT que usa SDK do dispositivo IoT do Microsoft Azure para Java* para se comunicar com o Hub IoT do Azure."
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
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Criar um aplicativo cliente do Hub IoT do Azure usando o Java

Este artigo descreve como criar um aplicativo cliente que usa o [SDK do dispositivo IoT do Microsoft Azure para Java][lnk-java-sdk] para se comunicar com o Hub IoT do Azure. O tutorial mostra como criar e compilar o projeto usando a ferramenta [Maven][apache-maven]. Você pode seguir estas instruções em um computador com Windows ou Linux.

Você pode exibir os [documentos de API Java][lnk-java-api-docs] para referência.

## Instalação

Veja [Preparar o ambiente de desenvolvimento][devbox-setup] para obter informações sobre os pré-requisitos e configurar seu ambiente de desenvolvimento no Windows ou Linux.

> [AZURE.NOTE]É importante que você conclua as etapas em [Preparar o ambiente de desenvolvimento][devbox-setup] antes de iniciar este tutorial para instalar os pré-requisitos e adicionar os arquivos JAR necessários ao seu repositório local do Maven.

## Criar o projeto

1. Na ferramenta de linha de comando, execute o seguinte comando para criar um novo projeto do Maven vazio em um local adequado em seu computador de desenvolvimento:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]Esse é um comando único e longo. Certifique-se de copiar o comando completo para colá-lo na ferramenta de linha de comando.

    Este comando cria uma pasta de projeto chamada *iot-device*, que tem a estrutura de projeto padrão do Maven. Para obter mais informações, veja [Maven em 5 minutos][maven-five-minutes] no site do Apache.

2. Abra o arquivo **pom.xml** na pasta iot-device em um editor de texto.

3. Adicione a seguinte nova seção **dependency** após uma seção existente para adicionar as bibliotecas de cliente necessárias:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. Adicione a seção **build** a seguir após a seção **dependencies** para que o arquivo JAR final adicione as dependências e o manifesto identifique a classe **main**.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Salve o arquivo **pom.xml**.

## Criar o aplicativo

1. Abra o arquivo **App.java** na pasta iot-device/src/main/java/com/mycompany/app em um editor de texto.

2. Adicione as seguintes instruções **import**, que incluem as bibliotecas de dispositivos IoT após a instrução **package**:

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
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. Adicione a classe **EventCallback** a seguir como uma classe aninhada dentro da class **App**. O método **execute** na classe **EventCallback** é chamado quando o dispositivo recebe uma confirmação do seu hub IoT em resposta a uma mensagem do dispositivo para a nuvem.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. Adicione a classe **MessageCallback** a seguir como uma classe aninhada dentro da class **App**. O método **execute** na classe **MessageCallback** permite que você envie uma mensagem de comentário para o hub IoT em resposta a uma mensagem da nuvem para o dispositivo recebida pelo dispositivo.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. Substituir o método **main** pelo seguinte código que:

  - Cria uma instância **DeviceClient**.
  - Inicializa o retorno de chamada de mensagem para mensagens da nuvem para o dispositivo.
  - Abre o **DeviceClient** para habilitá-lo para enviar mensagens do dispositivo para a nuvem e receber mensagens da nuvem para o dispositivo.
  - Envia dez mensagens de exemplo para o seu hub IoT.

    Substitua `<your device connection string>` por uma cadeia de conexão válida de dispositivo. Você pode provisionar um dispositivo e recuperar sua cadeia de conexão usando a ferramenta [DeviceExplorer][lnk-device-explorer] ou [Gerenciador do Hub IoT][lnk-iothub-explorer].

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. Para compilar o código e empacotá-lo em um arquivo JAR, execute o seguinte comando em um prompt de comando na pasta **iot-device** do projeto:

    ```
    mvn package
    ```

7. Para executar o aplicativo, execute o seguinte comando em um prompt de comando na pasta **iot-device** do projeto:

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. Você pode usar a ferramenta [DeviceExplorer][lnk-device-explorer] para monitorar as mensagens do dispositivo para a nuvem recebidas pelo hub IoT e para enviar mensagens da nuvem para o dispositivo do seu hub IoT.

## Alterar granularidade de registro em log

Para alterar a granularidade de registro em log, inclua a seguinte linha em seu arquivo `config.properties`:

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]Você pode encontrar uma explicação sobre os diferentes [níveis de log](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html).

Em seguida, defina a propriedade JVM `java.util.logging.config.file={Path to your config.properties file}`.

Para registrar os quadros AMQP, defina a variável de ambiente `PN_TRACE_FRM=1` no ambiente de comando.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1217_2015-->