---
title: "Introdução ao SDK do Azure IoT Gateway (Windows) |Microsoft Docs"
description: "Como criar um gateway em um computador Windows e saber mais sobre os principais conceitos no SDK do Azure IoT Gateway, tais como módulos e arquivos de configuração JSON."
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
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: f3cc32daac5059e816c885c88f4a7d36b6fc897e
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-windows"></a>Explorar a arquitetura do SDK do Gateway IoT no Windows

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar a amostra

Antes de começar, é necessário [configurar seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Windows.

1. Abra um **Prompt de comando do desenvolvedor para VS 2015** ou **Prompt de comando do desenvolvedor para VS 2017**.
1. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
1. Execute o scpript **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio e compila a solução. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **azure-iot-gateway-sdk**. Parâmetros adicionais podem ser fornecidos ao script para criar e executar testes de unidade e de ponta a ponta. Esses parâmetros são **--run-unittests** e **--run-e2e-tests**, respectivamente.

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.cmd** cria uma pasta chamada **build** na cópia local do repositório. Esta pasta contém os dois módulos usados nesta amostra.

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

1. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk**.

1. Execute o comando a seguir:

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

