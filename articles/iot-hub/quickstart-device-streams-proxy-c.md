---
title: Início rápido do C para fluxos de dispositivos do Hub IoT do Azure para SSH/RDP (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará um aplicativo C de exemplo que atua como proxy para habilitar cenários de SSH/RDP em fluxos de dispositivos do Hub IoT.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 300b42c9452fc58c857d075a7fd8c42fd6a1c409
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731726"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Início Rápido: SSH/RDP em fluxos de dispositivos do Hub IoT usando o aplicativo proxy C (versão prévia)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Consulte [esta página](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) para ter uma visão geral da configuração.

Este documento descreve a configuração para colocar o tráfego SSH em um túnel (usando a porta 22) por meio de fluxos de dispositivos. A instalação para o tráfego RDP é semelhante e requer uma simples alteração na configuração. Como os fluxos de dispositivos são independentes de protocolo e de aplicativo, este início rápido pode ser modificado (alterando-se as portas de comunicação) para acomodar outros tipos de tráfego do aplicativo.

## <a name="how-it-works"></a>Como ele funciona?
A figura abaixo ilustra a configuração de como os programas proxy no local do dispositivo e do serviço habilitarão a conectividade de ponta a ponta entre processos do cliente SSH e do daemon SSH. Durante a versão prévia pública, o SDK do C dá suporte somente a fluxos de dispositivos no lado do dispositivo. Consequentemente, este início rápido contém apenas instruções para executar o aplicativo proxy no local do dispositivo. Você deve executar um aplicativo proxy no local do serviço que acompanha, que está disponível nos guias [Início rápido do C#](./quickstart-device-streams-proxy-csharp.md) ou [Início rápido do Node.js](./quickstart-device-streams-proxy-nodejs.md).

![Texto Alt](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "Configuração de proxy local")


1. O proxy no local de serviço se conecta ao Hub IoT e inicia um fluxo de dispositivos para o dispositivo de destino.

2. O proxy no local do dispositivo conclui o handshake de inicialização do fluxo e estabelece um túnel de streaming de ponta a ponta através do ponto de extremidade de streaming do Hub IoT até o lado do serviço.

3. O proxy no local do dispositivo se conecta ao daemon SSH (SSHD) que escuta na porta 22 do dispositivo (é configurável, como descrito [abaixo](#run-the device-local-proxy-application)).

4. O proxy no local do serviço aguarda novas conexões SSH do usuário, escutando em uma porta designada – no caso, a porta 2222 (também é configurável, como descrito [abaixo](#run-the-device-local-proxy-application)). Quando o usuário se conecta por meio do cliente SSH, o túnel habilita o tráfego de aplicativo SSH a ser transferido entre os programas de cliente e servidor SSH.

> [!NOTE]
> O tráfego SSH que será enviado por um fluxo de dispositivos será encapsulado através do ponto de extremidade de streaming do Hub IoT, em vez de ser enviado diretamente entre o serviço e o dispositivo. Isso proporciona [esses benefícios](./iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon SSH em execução no mesmo dispositivo (ou computador) que o proxy no local do dispositivo. Neste início rápido, o fornecimento do endereço IP do daemon SSH permite que o daemon e o proxy no local do dispositivo também sejam executados em computadores diferentes.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho ["Desenvolvimento de área de trabalho com C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você usará o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md). Você preparará um ambiente de desenvolvimento usado para clonar e criar o [SDK do C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) no GitHub. O SDK no GitHub inclui o código de exemplo usado neste início rápido. 


1. Baixe a versão 3.11.4 do [sistema de build CMake](https://cmake.org/download/) do [GitHub](https://github.com/Kitware/CMake/releases/tag/v3.11.4). Verifique o binário baixado usando o valor de hash criptográfico correspondente. O exemplo a seguir usou o Windows PowerShell para verificar o hash criptográfico da versão 3.11.4 da distribuição MSI x64:

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

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados em seu computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Atualmente, o tamanho desse repositório está em torno de 220 MB. Essa operação deve demorar alguns minutos.


3. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute o comando a seguir, que cria uma versão do SDK específica para a plataforma cliente de desenvolvimento. No Windows, uma solução do Visual Studio para o dispositivo simulado será gerada no diretório `cmake`. 

```
    # In Linux
    cmake ..
    make -j
```

No Windows, execute os comandos a seguir no Prompt de Comando do Desenvolvedor para o prompt do Visual Studio 2015 ou 2017:

```
    rem In Windows
    rem For VS2015
    cmake .. -G "Visual Studio 15 2015"

    rem Or for VS2017
    cmake .. -G "Visual Studio 15 2017"

    rem Then build the project
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


## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por fluxos de dispositivos

### <a name="run-the-device-local-proxy-application"></a>Executar o aplicativo de proxy no local do dispositivo

- Edite o arquivo de origem `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` e forneça a cadeia de conexão do dispositivo, o IP/nome do host do dispositivo de destino, assim como a porta 22 do RDP:
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[Connection string of IoT Hub]";
  static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
  static const size_t localPort = 22; // Port of the local server to connect to.
```

- Compile o exemplo conforme segue:

```
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    make -j
```

```
    rem In Windows
    rem Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
```

- Execute o programa compilado no dispositivo:
```
    # In Linux
    # Go to sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    ./iothub_client_c2d_streaming_proxy_sample
```

```
    rem In Windows
    rem Go to sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
```

### <a name="run-the-service-local-proxy-application"></a>Executar o aplicativo de proxy no local do serviço

Conforme discutido [acima](#how-it-works), o estabelecimento de um fluxo de ponta a ponta para colocar o tráfego SSH em um túnel requer um proxy local em cada extremidade (ou seja, serviço e dispositivo). Durante a versão prévia pública, o SDK do C do Hub IoT dá suporte somente a fluxos de dispositivos no lado do dispositivo. Para o proxy no local de serviço, utilize os guias que acompanham em [início rápido do C#](./quickstart-device-streams-proxy-csharp.md) ou [início rápido do Node.js](./quickstart-device-streams-proxy-nodejs.md).


### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão SSH

Supondo que os proxies no local do dispositivo e do serviço estão em execução, utilize o programa de cliente SSH e se conecte ao proxy no local de serviço na porta 2222 (em vez do daemon SSH diretamente). 

```
ssh <username>@localhost -p 2222
```

Neste ponto, você verá o prompt de logon do SSH para inserir suas credenciais.


Saída do console no proxy no local do dispositivo que se conecta ao daemon SSH em `IP_address:22`: ![Texto Alt](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Saída de proxy no local do dispositivo")

Saída do console do programa do cliente SSH (o cliente SSH se comunica com o daemon SSH conectando-se à porta 22 na qual o proxy do local de serviço está escutando): ![Texto Alt](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Saída do cliente SSH")

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um Hub IoT, registrou um dispositivo, implantou um programa proxy no local do dispositivo e do serviço para estabelecer um fluxo de dispositivos através do Hub IoT e utilizou os proxies para colocar o tráfego SSH em um túnel.

Use os links abaixo para saber mais sobre fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
