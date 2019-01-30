---
title: Início rápido do C para fluxos de dispositivos do Hub IoT do Azure (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará aplicativos do lado do serviço do C que se comunica com um dispositivo IoT por meio de um fluxo de dispositivos.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830728"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início Rápido: comunicar-se com um aplicativo de dispositivo no C por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Durante a versão prévia pública, o SDK do C dá suporte somente a fluxos de dispositivos no lado do dispositivo. Consequentemente, este início rápido contém apenas instruções para executar o aplicativo no lado do dispositivo. Você deve executar um proxy no lado do serviço que acompanha, que está disponível nos guias [Início rápido do C#](./quickstart-device-streams-echo-csharp.md) ou [Início rápido do Node.js](./quickstart-device-streams-echo-nodejs.md).

Neste início rápido, o aplicativo C no lado do dispositivo tem a funcionalidade a seguir:

* Estabelecer um fluxo de dispositivos para um dispositivo IoT.

* Receber os dados enviados do lado do serviço e reproduzi-los novamente.

O código demonstrará o processo de inicialização de um fluxo de dispositivos, além de como usar para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho ["Desenvolvimento de área de trabalho com C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você usará o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md). Você preparará um ambiente de desenvolvimento usado para clonar e criar o [SDK do C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) no GitHub. O SDK no GitHub inclui o código de exemplo usado neste início rápido. 


1. Faça o download da versão 3.11.4 do [sistema de build CMake](https://cmake.org/download/). Verifique o binário baixado usando o valor de hash criptográfico correspondente. O exemplo a seguir usou o Windows PowerShell para verificar o hash criptográfico da versão 3.11.4 da distribuição MSI x64:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Os valores de hash a seguir para a versão 3.11.4 foram listados no site do CMake no momento da redação deste material:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

2. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Atualmente, o tamanho desse repositório está em torno de 220 MB.

3. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Execute o comando a seguir, que cria uma versão do SDK específica para a plataforma cliente de desenvolvimento. No Windows, uma solução do Visual Studio para o dispositivo simulado será gerada no diretório `cmake`. 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

No Windows, execute os comandos a seguir no Prompt de Comando do Desenvolvedor para o prompt do Visual Studio 2015 ou 2017:

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

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


## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Para executar o aplicativo do lado do dispositivo, você precisará executar as etapas a seguir:
- Configure seu ambiente de desenvolvimento, usando as instruções contidas neste [artigo sobre fluxos de dispositivos](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c).

- Forneça suas credenciais do dispositivo editando o arquivo de origem `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` e forneça a cadeia de conexão do dispositivo.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- Compile o código conforme segue:

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- Execute o programa compilado:

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Como mencionado anteriormente, o SDK do C do Hub IoT dá suporte somente a fluxos de dispositivos no lado do dispositivo. Para aplicativo no lado do serviço, use os programas de serviço que acompanham, disponíveis nos guias de [início rápido do C#](./quickstart-device-streams-echo-csharp.md) ou [início rápido do Node.js](./quickstart-device-streams-echo-nodejs.md).


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo C e leu a telemetria do hub usando o Azure Cloud Shell.

Use os links abaixo para saber mais sobre fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)