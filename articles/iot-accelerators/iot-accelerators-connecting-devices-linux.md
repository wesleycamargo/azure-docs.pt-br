---
title: Provisionar os dispositivos Linux para monitoramento remoto em C – Azure | Microsoft Docs
description: Descreve como conectar um dispositivo ao acelerador de solução de Monitoramento Remoto usando um aplicativo escrito em C em execução no Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 5faa91f054e62e2b3d9d317efe57f2d3f659cee6
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829827"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Conecte seu dispositivo ao acelerador de solução de monitoramento remoto (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo físico ao acelerador de solução de monitoramento remoto.

Assim como acontece com a maioria dos aplicativos inseridos que são executados em dispositivos restritos, o código do cliente do aplicativo do dispositivo é escrito em C. Neste tutorial, você cria o aplicativo em um computador com Ubuntu (Linux).

## <a name="prerequisites"></a>Pré-requisitos

Para completar as etapas deste guia de instruções, você precisará de um dispositivo com Ubuntu versão 15.04 ou posterior. Antes de continuar, [configure o ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>Exibir o código

O [exemplo de código](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) usado neste guia está disponível no repositório GitHub de SDKs C do IoT do Azure.

### <a name="download-the-source-code-and-prepare-the-project"></a>Baixe o código-fonte e prepare o projeto

Para preparar o projeto, clone ou baixe o [repositório de SDKs C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) do GitHub.

A amostra está localizada na pasta **samples/solutions/remote_monitoring_client**.

Abra o arquivo **remote_monitoring** na pasta **samples/solutions/remote_monitoring_client** em um editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

As etapas a seguir descrevem como usar *CMake* para compilar o aplicativo cliente. O aplicativo cliente de monitoramento remoto foi criado como parte do processo de build para o SDK.

1. Edite o arquivo **remote_monitoring** para substituir `<connectionstring>` pela cadeia de conexão do dispositivo que você anotou no início deste guia de instruções, quando adicionou um dispositivo do acelerador de solução.

1. Navegue até a raiz da sua cópia clonada do [repositório de SDKs C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) e execute os seguintes comandos para compilar o aplicativo cliente:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Execute o aplicativo cliente e envie telemetria ao Hub IoT:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    O console exibe mensagens, como:

    - O aplicativo envia a telemetria de amostra para o acelerador de solução.
    - Responde a métodos invocados do painel de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
