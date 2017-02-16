---
title: Processar mensagens do Hub IoT do Azure da nuvem para o dispositivo (Java) | Microsoft Docs
description: "Como processar mensagens do dispositivo para a nuvem do Hub IoT lendo o ponto de extremidade compatível com um Hub IoT. Crie um aplicativo de serviço Java que usa uma instância do EventProcessorHost."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: e3e4ad430d8941a09543ce2dc97f8e449a39bced
ms.openlocfilehash: 5ede1fdd040b2f59383dda27d6fb26b87c2d7f02


---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Processar mensagens do Hub IoT do dispositivo para nuvem (Java)
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Outros tutoriais ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d]) mostram como usar a funcionalidade básica de mensagem do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT.

Este tutorial foi criado com base no código mostrado no tutorial [Introdução ao Hub IoT] e mostra como usar o roteamento de mensagens para processar mensagens do dispositivo para a nuvem de maneira escalonável. O tutorial ilustra como processar mensagens que exigem ação imediata do back-end da solução. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM. Por outro lado, as mensagens de ponto de dados simplesmente são alimentadas no mecanismo de análise. Por exemplo, a telemetria de temperatura de um dispositivo que deve ser armazenado para análise posterior é uma mensagem de ponto de dados.

Ao fim deste tutorial, você executará três aplicativos de console do Java:

* **simulated-device**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT] , que envia mensagens de ponto de dados do dispositivo para a nuvem a cada segundo e mensagens interativas do dispositivo para a nuvem a cada 10 segundos. Este aplicativo usa o protocolo AMQP para se comunicar com o Hub IoT.
* **read-d2c-messages** exibe a telemetria enviada pelo aplicativo de dispositivo simulado.
* **read-critical-queue** retira as mensagens críticas da fila do Barramento de Serviço conectada ao Hub IoT.

> [!NOTE]
> O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo simulado neste tutorial por um dispositivo físico e como conectar dispositivos a um Hub IoT, confira o [Centro de desenvolvedores do Azure IoT].
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Uma versão de trabalho completa do tutorial [Introdução ao Hub IoT] .
* Java SE 8. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.
* Maven 3.  <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Maven para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. <br/>Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>Enviar mensagens interativas de um aplicativo do dispositivo simulado
Nesta seção, você modificará o aplicativo de dispositivo simulado criado no tutorial [Introdução ao Hub IoT] para ocasionalmente enviar mensagens que exijam processamento imediato.

1. Use um editor de texto para abrir o arquivo simulated-device\src\main\java\com\mycompany\app\App.java. Este arquivo contém o código do aplicativo **simulated-device** que você criou no tutorial [Introdução ao Hub IoT] .
2. Substitua a classe **MessageSender** pelo seguinte código:
   
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
                    if (new Random() > 0.7) {
                        Message msg = new Message("This is a critical message.");
                        msg.setProperty("level", "critical");
                    } else {
                        Message msg = new Message(msgStr);
                    }
                    
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
   
    Isso adiciona aleatoriamente a propriedade `"level": "critical"` às mensagens enviadas pelo dispositivo simulado, que simula uma mensagem que exige ação imediata do back-end da solução. O aplicativo passa essas informações nas propriedades da mensagem, e não no corpo da mensagem, de modo que o Hub IoT pode rotear a mensagem para o destino apropriado.
   
   > [!NOTE]
   > Você pode usar as propriedades a fim de rotear as mensagens para vários cenários, incluindo processamento de ampliação, além do exemplo de afunilamento mostrado aqui.
   > 
   > 

2. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.
   
   > [!NOTE]
   > Para simplificar, esse tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como uma retirada exponencial, como sugerido no artigo [Tratamento de falhas transitórias]do MSDN.
   > 
   > 

3. Para compilar o aplicativo **simulated-device** usando o Maven, execute o comando a seguir no prompt de comando na pasta simulated-device:
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adicionar uma fila ao seu Hub IoT e rotear mensagens para ela
Nesta seção, você cria uma fila do Barramento de Serviço, conecta-a ao Hub IoT e configura o Hub IoT para enviar mensagens à fila com base na presença de uma propriedade na mensagem. Para saber mais sobre como processar mensagens das filas do Barramento de Serviço, confira [Introdução às filas][Service Bus queue].

1. Crie uma fila do Barramento de Serviço conforme descrito em [Introdução às filas][Service Bus queue]. Anote o namespace e o nome da fila.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de Extremidade**.
    
    ![Pontos de extremidade no Hub IoT][30]

3. Na folha de pontos de extremidade, clique em **Adicionar** na parte superior para adicionar a fila ao Hub IoT. Chame o ponto de extremidade de "CriticalQueue" e use o menu suspenso para selecionar a **Fila do Barramento de Serviço**, o namespace do Barramento de Serviço no qual suas filas estão e o nome da sua fila. Quando terminar, clique em **Salvar** na parte inferior.
    
    ![Adicionando um ponto de extremidade][31]
    
4. Agora clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **DeviceTelemetry** como a fonte dos dados. Insira `level="critical"` como a condição e escolha a fila que acabou de adicionar como um ponto de extremidade como o ponto de extremidade da rota. Quando terminar, clique em **Salvar** na parte inferior.
    
    ![Adicionando uma rota][32]
    
    Verifique se a rota de fallback está definida como ATIVADA. Essa é a configuração padrão do Hub IoT.
    
    ![Rota de fallback][33]


## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Ler no ponto de extremidade da fila
Como opção, você pode ler as mensagens no ponto de extremidade da fila seguindo as instruções em [Introdução às filas][lnk-sb-queues-java]. Dê ao aplicativo o nome de **read-critical-queue**.

## <a name="run-the-applications"></a>Executar os aplicativos
Agora, você está pronto para executar os três aplicativos.

1. Para executar o aplicativo **read-d2c-messages**, em um prompt ou shell de comando, navegue até a pasta read-d2c e execute o seguinte comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar read-d2c-messages][readd2c]
2. Para executar o aplicativo **read-critical-queue**, em um prompt ou shell de comando, navegue até a pasta read-critical-queue e execute o seguinte comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar read-critical-messages][readqueue]

3. Para executar o aplicativo **simulated-device** , em um prompt de comando ou shell, navegue até a pasta simulated-device e execute o seguinte comando:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar simulated-device][simulateddevice]


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a expedir confiavelmente mensagens do dispositivo para nuvem usando a funcionalidade de roteamento de mensagens do Hub IoT.


O tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d] mostra como enviar mensagens da solução de back-end para seus dispositivos.

Para ver exemplos de soluções completas que usam o Hub IoT, veja [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md
[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introdução ao Hub IoT]: iot-hub-java-java-getstarted.md
[Centro de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub



<!--HONumber=Jan17_HO2-->


