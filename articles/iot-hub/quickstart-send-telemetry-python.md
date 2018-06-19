---
title: 'Início Rápido: Enviar telemetria ao Hub IoT do Azure (Python) | Microsoft Docs'
description: Neste início rápido, você executa um aplicativo Python de exemplo para enviar telemetria simulada a um hub IoT e usar um utilitário para ler a telemetria do hub IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: a6bece43f003ed5ddd2db2b03f12c02c1ccaa28e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636266"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-python"></a>Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT, e ler a telemetria do hub com um aplicativo de back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do Hub IoT, para um aplicativo de back-end para processamento.

Este início rápido usa um aplicativo Python previamente codificado para enviar a telemetria, e um utilitário CLI para ler a telemetria do Hub. Antes de executar esses dois aplicativos, você cria um hub IoT e registra um dispositivo com o hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

Os dois exemplos de aplicativo executados neste início rápido são escritos usando Python. Você precisa do Python 2.7.x ou 3.5.x no computador de desenvolvimento.

Você pode fazer o download do Python para várias plataformas a partir do [Python.org](https://www.python.org/downloads/).

Você pode verificar a versão atual do Python no computador de desenvolvimento usando um dos seguintes comandos:

```python
python --version
```

```python
python3 --version
```

Faça o download do projeto de exemplo de Python do https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip e extraia o arquivo ZIP.

Para instalar o utilitário CLI que lê a telemetria do Hub IoT, primeiro instale o Node.js v4.x.x ou posterior em seu computador de desenvolvimento. Você pode fazer o download do Node.js para várias plataformas a partir do [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

Para instalar o utilitário CLI do `iothub-explorer`, execute o seguinte comando:

```cmd/sh
npm install -g iothub-explorer
```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa a CLI do Azure para registrar um dispositivo simulado.

1. Adicione a extensão CLI do Hub IoT e crie a identidade do dispositivo. Substitua `{YourIoTHubName}` pelo nome escolhido para o seu hub IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

    Se você escolher um nome diferente para seu dispositivo, atualize o nome de dispositivo nos aplicativos de exemplo antes de executá-lo.

1. Execute o seguinte comando para obter a _cadeia de conexão de dispositivo_ do dispositivo que você acabou de registrar:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anote a cadeia de conexão do dispositivo, que se parece com `Hostname=...=`. Você usará esse valor posteriormente no início rápido.

1. Você também precisa de uma _cadeia de conexão de serviço_ para permitir que a CLI do `iothub-explorer` se conecte ao seu Hub IoT e recupere as mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Anote a cadeia de conexão de serviço, que se parece com `Hostname=...=`. Você usará esse valor posteriormente no início rápido. A cadeia de conexão do serviço é diferente da cadeia de conexão do dispositivo.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Em uma janela de terminal, navegue até a pasta raiz do projeto Python de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.py** em seu editor de texto preferido.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve as alterações no arquivo **SimulatedDevice.py**.

1. Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias para o aplicativo de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Na janela de terminal, execute os seguintes comandos para executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A captura de tela a seguir mostra a saída à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O utilitário CLI `iothub-explorer` se conecta ao ponto de extremidade **Eventos** do lado do serviço no Hub IoT. O utilitário recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

Em outra janela de terminal, execute os comandos a seguir substituindo `{your hub service connection string}` com a cadeia de conexão de serviço que você anotou anteriormente:

```cmd/sh
iothub-explorer monitor-events MyPythonDevice --login {your hub service connection string}
```

A captura de tela a seguir mostra a saída à medida que o utilitário recebe a telemetria enviada pelo dispositivo simulado para o hub:

![Executar o aplicativo de back-end](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja concluir o próximo início rápido, mantenha o grupo de recursos e o hub IoT e reutilize-os posteriormente.

Se você não precisar mais do hub IoT, exclua-o junto com o grupo de recursos no portal. Para isso, selecione o grupo de recursos **qs-iot-hub-rg** que contém o Hub IoT e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Python e leu a telemetria do hub usando um aplicativo de back-end simples.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-python.md)