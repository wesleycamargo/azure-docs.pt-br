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
ms.date: 08/25/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23176a9251a90a985a5d2fbce23ceeb9d0925234


---
# <a name="azure-iot-gateway-sdk-beta-get-started-using-linux"></a>SDK do Gateway IoT do Azure (beta) - Introdução usando o Linux
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
   
    O script de build coloca **liblogger_hl.so** na pasta **build/modules/logger/** e **libhello_world_hl.so** na pasta **build/modules/hello_world/**. Use esses caminhos para o valor de **caminho do módulo** , conforme mostrado no arquivo de configurações do JSON abaixo.
2. O arquivo **hello_world_lin.json** na pasta **samples/hello_world/src** é um arquivo de configurações do JSON de exemplo para o Linux que pode ser usado para executar a amostra. As definições do JSON de exemplo mostradas abaixo pressupõem que você executará a amostra da raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Para o módulo **logger_hl**, na seção **args**, defina o valor de **filename** para o nome e caminho do arquivo que conterá os dados de log.
   
    Este é um exemplo de um arquivo de configurações do JSON para o Linux que gravará no **log.txt** , na pasta em que a amostra é executada.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "loading args": {
            "module path" : "./build/modules/logger/liblogger_hl.so"
          },
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "loading args": {
            "module path" : "./build/modules/hello_world/libhello_world_hl.so"
          },
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. No shell, navegue até a pasta **azure-iot-gateway-sdk** .
5. Execute o comando a seguir:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO2-->


