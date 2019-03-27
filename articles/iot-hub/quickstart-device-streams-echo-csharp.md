---
title: Início rápido do C# para fluxos de dispositivos do Hub IoT do Azure (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará dois aplicativos de exemplo do C# que se comunicam por fluxo de dispositivos estabelecido através do Hub IoT.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 0b39943b318afd6f9aabd6ab0711651d64e975cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121597"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Início rápido: comunicar-se com aplicativos de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o Hub IoT do Microsoft Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Este início rápido envolve dois programas C# que utilizam fluxos de dispositivos para enviar dados (reproduzir).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com os Hubs IoT criados nas seguintes regiões:

  - **Centro dos EUA**
  - **EUA Central EUAP**

Os dois exemplos de aplicativo executados neste início rápido são escritos usando o C#. É necessário ter o SDK do .NET Core 2.1.0 ou maior no computador de desenvolvimento.

Você pode fazer o download do SDK do .NET Core para várias plataformas a partir do [.NET](https://www.microsoft.com/net/download/all).

Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

```
dotnet --version
```

Faça o download do projeto de exemplo em C# do https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP. Você precisará dele no lado do dispositivo e no lado do serviço.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e criar a identidade do dispositivo. 

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyDevice**: Esse é o nome fornecido para o dispositivo registrado. Use MyDevice, conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anote a cadeia de conexão do dispositivo, que se parece com o exemplo a seguir:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

3. Você também precisa de _cadeia de conexão de serviço_ do Hub IoT para habilitar o aplicativo do lado do serviço para se conectar ao Hub IoT e estabelecer um fluxo de dispositivos. O comando a seguir recupera esse valor para o Hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, que tem esta aparência:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Navegue até `iot-hub/Quickstarts/device-streams-echo/service` na pasta do projeto descompactada. Você precisará das informações a seguir:

| Nome do parâmetro | Valor de parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | Forneça a cadeia de conexão de serviço do seu Hub IoT. |
| `DeviceId` | Forneça a ID do dispositivo criada anteriormente, por exemplo, MyDevice. |

Compile e execute o código conforme segue:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Um tempo limite ocorre se o aplicativo do lado do dispositivo não responde a tempo.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Navegue até o diretório `iot-hub/Quickstarts/device-streams-echo/device` na pasta do projeto descompactada. Você precisará das informações a seguir:

| Nome do parâmetro | Valor de parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | Forneça a cadeia de conexão do dispositivo do seu Hub IoT. |

Compile e execute o código conforme segue:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

No final da última etapa, o programa do lado do serviço iniciará um fluxo para seu dispositivo e, após o estabelecimento, enviará um buffer de cadeia de caracteres para o serviço pelo fluxo. Neste exemplo, o programa no lado do serviço simplesmente reproduz os mesmos dados para o dispositivo, demonstrando uma comunicação bidirecional de sucesso entre os dois aplicativos. Veja a figura abaixo.

Saída do console no lado do dispositivo: ![Texto Alt](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Saída do console no lado do dispositivo")

Saída do console no lado do serviço: ![Texto Alt](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Saída do console no lado do serviço")

O tráfego que está sendo enviado pelo fluxo será ser encapsulado por meio do Hub IoT em vez de ser enviado diretamente. Isso proporciona [esses benefícios](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicativos C# no lado do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
