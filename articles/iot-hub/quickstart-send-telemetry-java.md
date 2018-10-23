---
title: 'Início Rápido: Enviar telemetria ao Hub IoT do Azure (Java) | Microsoft Docs'
description: Neste início rápido, você executará dois aplicativos Java de exemplo para enviar telemetria simulada para um Hub IoT e ler telemetria do Hub IoT para processamento na nuvem.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/22/2018
ms.author: dobett
ms.openlocfilehash: a4840fee22086bf6716f5f83ba86c4ac464377f9
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364397"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-java"></a>Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT, e ler a telemetria do hub com um aplicativo de back-end (Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do Hub IoT, para um aplicativo de back-end para processamento.

O início rápido usa dois aplicativos Java previamente escritos, um para enviar a telemetria e outro para ler a telemetria do hub. Antes de executar esses dois aplicativos, você cria um hub IoT e registra um dispositivo com o hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os dois exemplos de aplicativo executados neste início rápido são escritos usando o Java. Você precisa do Java SE 8 ou posterior em seu computador de desenvolvimento.

Você pode fazer o download do Java para várias plataformas a partir do [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Verifique a versão atual do Java no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
java --version
```

Para criar os exemplos, você precisa instalar o Maven 3. Você pode fazer o download do Maven para várias plataformas a partir do [Apache Maven](https://maven.apache.org/download.cgi).

Você pode verificar a versão atual do Maven no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
mvn --version
```

Faça o download do projeto de exemplo em Java do https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e criar a identidade do dispositivo. 

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

   **MyJavaDevice**: esse é o nome fornecido para o dispositivo registrado. Use MyJavaDevice conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar: **NomeDoSeuHubIoT** : substitua esse espaço reservado abaixo pelo nome escolhido para seu Hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyJavaDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

3. Você também precisa do _ponto de extremidade compatível com Hubs de Evento_, _caminho compatível dos Hubs de Evento_ e _Chave primária iothubowner_ de seu hub IoT para ativar o aplicativo back-end a fim de se conectar ao seu hub IoT e recuperar as mensagens. Os comandos a seguir recuperam esses valores para o seu hub IoT:

     **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name YourIoTHubName

    az iot hub show --query properties.eventHubEndpoints.events.path --name YourIoTHubName

    az iot hub policy show --name iothubowner --query primaryKey --hub-name YourIoTHubName
    ```

    Anote esses três valores, que você usará posteriormente no início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto Java de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

2. Abra o arquivo **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** em um editor de texto de sua escolha.

    Substitua o valor da variável `connString` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve as alterações no arquivo **SimulatedDevice.java**.

3. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e compile o aplicativo de dispositivo simulado:

    ```cmd/sh
    mvn clean package
    ```

4. Na janela de terminal local, execute os seguintes comandos para executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O aplicativo de back-end se conecta ao ponto de extremidade **Eventos** do lado do serviço em seu Hub IoT. O aplicativo recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

1. Em outra janela de terminal local, navegue até a pasta raiz do projeto Java de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\read-d2c-messages**.

2. Abra o arquivo **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** em um editor de texto de sua escolha. Atualize as variáveis a seguir e salve suas alterações no arquivo.

    | Variável | Valor |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Substitua o valor da variável pelo ponto de extremidade compatível com os Hubs de Eventos que você anotou anteriormente. |
    | `eventHubsCompatiblePath`     | Substitua o valor da variável pelo caminho compatível com os Hubs de Eventos que você anotou anteriormente. |
    | `iotHubSasKey`                | Substitua o valor da variável pela chave primária iothubowner que você anotou anteriormente. |


3. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e compilar o aplicativo de back-end:

    ```cmd/sh
    mvn clean package
    ```

4. Na janela de terminal local, execute os comandos a seguir para executar o aplicativo back-end:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A captura de tela a seguir mostra a saída à medida que o aplicativo de back-end recebe a telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar o aplicativo de back-end](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Java e leu a telemetria do hub usando um aplicativo de back-end simples.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-java.md)