---
title: "Introdução ao SDK do Gateway do Hub IoT | Microsoft Docs"
description: "Este passo a passo do SDK do Gateway IoT do Azure usa o Linux para ilustrar os principais conceitos que você deve compreender ao usar o SDK do Gateway IoT do Azure."
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
ms.date: 11/23/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: a76320718f0cefa015728cb79df944e0d34bbf74
ms.openlocfilehash: cbb909adc2d29f9b80a4c97d06176fe74b64a75a


---
# <a name="azure-iot-gateway-sdk---get-started-using-linux"></a>SDK do Gateway IoT do Azure – Introdução usando o Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar a amostra
Antes de começar, é necessário [configurar seu ambiente de desenvolvimento][lnk-setupdevbox] para funcionar com o SDK no Linux.

1. Abra um shell.
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Execute o script **tools/build.sh** . Esse script usa o utilitário **cmake** para criar uma pasta chamada **build** na pasta raiz da cópia local do repositório **azure-iot-gateway-sdk** e gerar um makefile. O script então cria a solução e executa os testes.

> [!NOTE]
> Sempre que você executa o script **build.sh**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Como executar a amostra
1. O script **build.sh** gera sua saída na pasta **build** na cópia local do repositório **azure-iot-gateway-sdk**. Isso inclui os dois módulos usados nesta amostra.
   
    O script de build coloca **liblogger.so** na pasta **build/modules/logger/** e **libhello_world.so** na pasta **build/modules/hello_world/**. Use esses caminhos para o valor de **caminho do módulo** , conforme mostrado no arquivo de configurações do JSON abaixo.
2. O processo de hello_world_sample leva o caminho até um arquivo de configuração JSON como um argumento na linha de comando. Um exemplo de arquivo JSON foi fornecido como parte do repositório em **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** e está copiado abaixo. Ele funcionará da forma como está, a menos que você tenha modificado o script de compilação e colocado módulos ou exemplo de executáveis em locais não padrão.

   > [!NOTE]
   > Os caminhos do módulo são relativos ao diretório de trabalho atual de onde o executável hello_world_sample é iniciado, não ao diretório onde o executável está localizado. O arquivo de configuração do JSON de exemplo assume o padrão de gravar 'log.txt' no diretório de trabalho atual.
   
    ```
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
3. Navegue até a pasta **azure-iot-gateway-sdk**.
4. Execute o comando a seguir:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO4-->


