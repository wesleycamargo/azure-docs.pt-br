---
title: "Provisionar os dispositivos Windows para monitoramento remoto em C – Azure | Microsoft Docs"
description: "Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em execução no Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2017
ms.author: dobett
ms.openlocfilehash: ef38517b55b352acf036e62d407f1ff840d6f804
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo físico à solução de monitoramento remoto pré-configurada.

## <a name="create-a-c-client-solution-on-windows"></a>Criar uma solução cliente C no Windows

Assim como acontece com a maioria dos aplicativos inseridos que são executados em dispositivos restritos, o código do cliente do aplicativo do dispositivo é escrito em C. Neste tutorial, você cria o aplicativo em um computador com Windows.

### <a name="create-the-starter-project"></a>Criar o projeto inicial

Crie um projeto inicial no Visual Studio 2017 e adicione os pacotes NuGet de cliente do dispositivo Hub IoT:

1. No Visual Studio, crie um aplicativo de console C usando o modelo **Aplicativo do Console Windows** do Visual C++. Dê o nome de **RMDevice**ao projeto.

    ![Criar aplicativo de console do Windows do Visual C++](media/iot-suite-connecting-devices/visualstudio01.png)

1. No **Gerenciador de Soluções**, exclua os arquivos `stdafx.h`, `targetver.h` e `stdafx.cpp`.

1. No **Gerenciador de Soluções**, renomeie os arquivos `RMDevice.cpp` e `RMDevice.c`.

    ![Gerenciador de Soluções mostrando o arquivo RMDevice.c renomeado](media/iot-suite-connecting-devices/visualstudio02.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice** e clique em **Gerenciar Pacotes NuGet**. Clique em **Procurar** para pesquisar e instalar os seguintes pacotes NuGet:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![O gerenciador de pacotes do NuGet mostra os pacotes Microsoft.Azure.IoTHub instalados](media/iot-suite-connecting-devices/visualstudio03.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice** e clique em **Propriedades** para abrir a caixa de diálogo **Páginas de Propriedade** do projeto. Para obter detalhes, confira [Configurando as propriedades do projeto no Visual C++](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Escolha a pasta **C/C++**, selecione a página de propriedades **Cabeçalhos pré-compilados**.

1. Defina **Cabeçalho pré-compilado** como **Não usar cabeçalhos pré-compilados**. Depois, escolha **Aplicar**.

    ![As propriedades do projeto mostram projeto não usando cabeçalhos pré-compilados](media/iot-suite-connecting-devices/visualstudio04.png)

1. Escolha a pasta **Vinculador** e selecione a página de propriedades **Entrada**.

1. Adicione `crypt32.lib` à propriedade **Dependências Adicionais**. Clique em **OK** e **OK** novamente para salvar os valores da propriedade do projeto.

    ![As propriedades do projeto mostram o vinculador incluindo crypt32.lib](media/iot-suite-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Adicionar a biblioteca Parson JSON

Adicione a biblioteca Parson JSON ao projeto **RMDevice** e adicione as instruções `#include` necessárias:

1. Em uma pasta adequada no computador, clone o repositório Parson GitHub usando o seguinte comando:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Copie os arquivos `parson.h` e `parson.c` da cópia local do repositório Parson para a pasta do projeto **RMDevice**.

1. No Visual Studio, clique com o botão direito do mouse no projeto **RMDevice**, escolha **Adicionar** e escolha **Item Existente**.

1. Na caixa de diálogo **Adicionar Item Existente**, selecione os arquivos `parson.h` e `parson.c` na pasta do projeto **RMDevice**. Em seguida, escolha **Adicionar** para adicionar esses dois arquivos ao projeto.

    ![O Gerenciador de Soluções mostra os arquivos parson.h e parson.c](media/iot-suite-connecting-devices/visualstudio06.png)

1. No Visual Studio, abra o arquivo `RMDevice.c`. Substitua as instruções `#include` existentes pelo seguinte código:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Agora você pode verificar se o projeto tem as dependências corretas configuradas compilando a solução.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Adicione código para invocar a função **remote\_monitoring\_run** e compile e execute o aplicativo do dispositivo:

1. Para invocar a função **remote\_monitoring\_run**, substitua a função **main** pelo seguinte código:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Escolha **Compilar** e **Compilar Solução** para compilar o aplicativo do dispositivo. Ignore o aviso sobre a função **gmtime**.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice**, escolha **Depurar** e **Iniciar nova instância** para executar a amostra. O console exibe mensagens, como:

    * O aplicativo envia a telemetria de amostra para a solução pré-configurada.
    * Recebe valores de propriedade desejados definidos no painel de solução.
    * Responde a métodos invocados do painel de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
