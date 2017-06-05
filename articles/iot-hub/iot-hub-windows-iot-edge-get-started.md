---
title: "Introdução ao Azure IoT Edge (Windows) | Microsoft Docs"
description: "Como criar um gateway em do Azure IoT Edge um computador Windows e saber mais sobre os principais conceitos do Azure IoT Edge, como módulos e arquivos de configuração JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 117b949d4f8b84fe5ce43fc1dc38e3326bcfcfba
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorar a arquitetura do Azure IoT Edge no Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar a amostra

Antes de começar, é necessário [configurar seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Windows.

1. Abra um **Prompt de comando do desenvolvedor para VS 2015** ou **Prompt de comando do desenvolvedor para VS 2017**.
1. Navegue até a pasta raiz na sua cópia local do repositório **iot-edge**.
1. Execute o scpript **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio e compila a solução. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **iot-edge**. Parâmetros adicionais podem ser fornecidos ao script para criar e executar testes de unidade e de ponta a ponta. Esses parâmetros são **--run-unittests** e **--run-e2e-tests**, respectivamente.

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.cmd** cria uma pasta chamada **build** na cópia local do repositório. Esta pasta contém os dois módulos IoT Edge usados neste exemplo.

    O script de build coloca **logger.dll** na pasta **build\\modules\\logger\\Debug** e **hello\_world.dll** na pasta **build\\modules\\hello_world\\Debug**. Use esses caminhos para os valores de **caminho do módulo**, conforme mostrado no arquivo de configurações do JSON a seguir.
1. O processo hello\_world\_sample leva o caminho até um arquivo de configuração JSON como um argumento na linha de comando. O arquivo JSON de exemplo a seguir é fornecido no repositório SDK em **samples\\hello\_world\\src\\hello\_world\_win.json**. Este arquivo de configuração funciona da forma como está, a menos que você tenha modificado o script de compilação e colocado módulos ou exemplo de executáveis em locais não padrão.

   > [!NOTE]
   > Os caminhos de módulo são relativos ao diretório em que se encontra o hello\_world\_sample.exe. O arquivo de configuração do JSON de exemplo assume o padrão de gravar 'log.txt' no diretório de trabalho atual.

    ```json
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```

1. Navegue até a pasta raiz da sua cópia local do repositório **iot-edge**.

1. Execute o comando a seguir:

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

