---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e80033d696de1b83da43fc27e5be9eca3b3f8757
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168457"
---
## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, você usa a CLI do Azure para criar a identidade do dispositivo para este tutorial. A CLI do Azure é pré-instalada no [Azure Cloud Shell](https://docs.microsoft.com/zure/cloud-shell/overview), mas você também pode [instalá-la localmente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Execute o comando a seguir no ambiente de linha de comando em que você está usando a CLI do Azure para instalar a extensão de IoT:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Se você estiver executando a CLI do Azure localmente, use o seguinte comando para entrar sua conta do Azure (se estiver usando o Cloud Shell, você será conectado automaticamente e não precisará executar esse comando):

    ```cmd/sh
    az login
    ```

1. Por fim, crie uma nova identidade do dispositivo chamada `myDeviceId` e recupere a cadeia de conexão do dispositivo com estes comandos:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anote a cadeia de conexão do dispositivo no resultado. Essa cadeia de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu Hub IoT como um dispositivo.

<!-- images and links -->
