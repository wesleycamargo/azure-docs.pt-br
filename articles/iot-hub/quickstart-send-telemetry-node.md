---
title: Início rápido do envio de telemetria ao Hub IoT do Azure (Node.js) | Microsoft Docs
description: Neste início rápido, você executa dois aplicativos Node.js de exemplo para enviar telemetria simulada a um hub IoT e ler a telemetria do hub IoT para processamento na nuvem.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 572ecbfee6a3c361a2940d74b560910fd85b344b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635858"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-nodejs"></a>Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT, e ler a telemetria do hub com um aplicativo de back-end (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do Hub IoT, para um aplicativo de back-end para processamento.

O início rápido usa dois aplicativos Node.js previamente escritos, um para enviar a telemetria e outro para ler a telemetria do hub. Antes de executar esses dois aplicativos, você cria um hub IoT e registra um dispositivo com o hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

Os dois exemplos de aplicativo executados neste início rápido são escritos usando o Node.js. Você precisa do Node.js v4.x.x ou posterior em seu computador de desenvolvimento.

Você pode fazer o download do Node.js para várias plataformas a partir do [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

Faça o download do projeto de exemplo de Node.js do https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa a CLI do Azure para registrar um dispositivo simulado.

1. Adicione a extensão CLI do Hub IoT e crie a identidade do dispositivo. Substitua `{YourIoTHubName}` pelo nome escolhido para o seu hub IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

    Se você escolher um nome diferente para seu dispositivo, atualize o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

1. Execute o seguinte comando para obter a _cadeia de conexão de dispositivo_ do dispositivo que você acabou de registrar:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Anote a cadeia de conexão do dispositivo, que se parece com `Hostname=...=`. Você usará esse valor posteriormente no início rápido.

1. Você também precisa de uma _cadeia de conexão de serviço_ para permitir que aplicativos de back-end se conectem ao seu hub IoT e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Anote a cadeia de conexão de serviço, que se parece com `Hostname=...=`. Você usará esse valor posteriormente no início rápido. A cadeia de conexão do serviço é diferente da cadeia de conexão do dispositivo.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Em uma janela de terminal, navegue até a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.js** em seu editor de texto preferido.

    Substitua o valor da variável `connectionString` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve as alterações no arquivo **SimulatedDevice.js**.

1. Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A captura de tela a seguir mostra a saída à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O aplicativo de back-end se conecta ao ponto de extremidade **Eventos** do lado do serviço em seu Hub IoT. O aplicativo recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

1. Em outra janela de terminal, navegue até a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue até a pasta **read-d2c-messages**.

1. Abra o arquivo **iot-hub\Quickstarts\ReadDeviceToCloudMessages.js** em seu editor de texto preferido.

    Substitua o valor da variável `connectionString` pela cadeia de conexão do serviço que você anotou anteriormente. Depois, salve suas alterações no arquivo **ReadDeviceToCloudMessages.js**.

1. Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de back-end:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    A captura de tela a seguir mostra a saída à medida que o aplicativo de back-end recebe a telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar o aplicativo de back-end](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja concluir o próximo início rápido, mantenha o grupo de recursos e o hub IoT e reutilize-os posteriormente.

Se você não precisar mais do hub IoT, exclua-o junto com o grupo de recursos no portal. Para isso, selecione o grupo de recursos **qs-iot-hub-rg** que contém o Hub IoT e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Node.js e leu a telemetria do hub usando um aplicativo de back-end simples.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-node.md)