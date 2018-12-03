---
title: Enviar telemetria ao Hub IoT do Azure (Android) | Microsoft Docs
description: Neste início rápido, você executa um aplicativo Android de exemplo para enviar telemetria simulada a um hub IoT e ler a telemetria do hub IoT para processamento na nuvem.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2018
ms.author: wesmc
ms.openlocfilehash: 66c1380070c9f9732369cb0d209e428525d53ce8
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427643"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Início Rápido: Enviar telemetria de IoT de um dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste início rápido, você envia telemetria a um Hub IoT de um aplicativo do Android em execução em um dispositivo simulado ou físico.

O início rápido usa um aplicativo do Android previamente escrito para enviar a telemetria. A telemetria será lida no Hub IoT usando o Azure Cloud Shell. Antes de executar o aplicativo, crie um hub IoT e registre um dispositivo com o hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Android Studio de https://developer.android.com/studio/. Para obter mais informações sobre a instalação do Android Studio, confira [instalação do Android](https://developer.android.com/studio/install). 

* O SDK do Android 27 é usado pelo exemplo deste artigo. 

* O [exemplo de aplicativo Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) executado neste início rápido faz parte do repositório azure-iot-samples-java no Github. Baixe ou clone o repositório [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).



## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e criar a identidade do dispositivo. 

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

   **MyAndroidDevice**: esse é o nome fornecido para o dispositivo registrado. Use o MyAndroidDevice conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente neste início rápido para enviar a telemetria.

## <a name="send-telemetry"></a>Enviar telemetria

1. Abra o projeto Android de exemplo do github no Android Studio. O projeto está localizado no diretório a seguir da sua cópia baixada ou clonada do repositório [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle.properties* para o projeto de exemplo e substitua o espaço reservado **Device_Connection_String** pela cadeia de conexão de dispositivo que você anotou anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **Arquivo** > **Sincronizar projetos com arquivos do Gradle**. Verifique se que a compilação foi concluída.

4. Depois que a compilação for concluída, clique em **Executar** > **Executar “aplicativo”**. Configure o aplicativo para ser executado em um dispositivo Android físico ou em um emulador do Android. Para obter mais informações sobre como executar um aplicativo Android em um dispositivo físico ou emulador, confira [Executar seu aplicativo](https://developer.android.com/training/basics/firstapp/running-app).

5. Depois que o aplicativo for carregado, clique no botão **Iniciar** para iniciar o envio de telemetria ao Hub IoT:

    ![Aplicativo](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorar as mensagens de dispositivo enviadas pelo dispositivo Android.

1. Usando o Azure Cloud Shell, execute o seguinte comando para se conectar e ler mensagens do hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    A captura de tela a seguir mostra a saída à medida que o Hub IoT recebe a telemetria enviada pelo dispositivo Android:

      ![Leia as mensagens do dispositivo usando a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Android e leu a telemetria do hub usando o Azure Cloud Shell.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-android.md)

