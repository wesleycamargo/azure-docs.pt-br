---
title: "Introdução ao Edge IoT do Azure (Linux) |Microsoft Docs"
description: "Como criar um gateway em um computador Linux e saber mais sobre os principais conceitos Edge IoT do Azure, tais como módulos e arquivos de configuração JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c401052d65cb9f06398fcf6597dc1de5e7451734
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorar a arquitetura do Edge IoT do Azure no Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar a amostra

Antes de começar, você deve [configurar seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Linux.

1. Abra um shell.
1. Navegue até a pasta raiz na sua cópia local do repositório **iot-edge** .
1. Execute o script **tools/build.sh** . Esse script usa o utilitário **cmake** para criar uma pasta chamada **build** na pasta raiz da cópia local do repositório **iot-edge** e gerar um makefile. O script cria a solução, ignorando os testes de unidade e os testes de ponta a ponta. Adicione o parâmetro **--skip-unittests** se quiser criar e executar os testes de unidade. Adicione **--run-e2e-tests** se quiser compilar e executar os testes de ponta a ponta.

> [!NOTE]
> Sempre que você executa o script **build.sh**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **iot-edge**.

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.sh** gera sua saída na pasta **build** na cópia local do repositório **iot-edge**. Essa saída inclui os dois módulos do Edge IoT usados neste exemplo.

    O script de build coloca **liblogger.so** na pasta **build/modules/logger/** e **libhello\_world.so** na pasta **build/modules/hello_world/**. Use esses caminhos para o valor de **caminho do módulo**, conforme mostrado no arquivo de configurações do JSON de exemplo a seguir.
1. O processo hello\_world\_sample leva o caminho até um arquivo de configuração JSON como um argumento na linha de comando. O arquivo JSON de exemplo a seguir é fornecido no repositório SDK em **samples/hello\_world/src/hello\_world\_lin.json**. Este arquivo de configuração funciona da forma como está, a menos que você tenha modificado o script de compilação e colocado módulos ou exemplo de executáveis do Edge IoT em locais não padrão.

   > [!NOTE]
   > Os caminhos do módulo são relativos ao diretório de trabalho atual de onde o executável hello\_world\_sample é iniciado, não ao diretório onde o executável está localizado. O arquivo de configuração do JSON de exemplo assume o padrão de gravar 'log.txt' no diretório de trabalho atual.

    ```json
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links":
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
1. Navegue para **criar** pasta.
1. Execute o comando a seguir:

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

