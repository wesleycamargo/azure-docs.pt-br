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
ms.date: 11/16/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: e1cf5ed3f2434a9e98027afd0225207ad5d2f1b1
ms.openlocfilehash: 3d9997655e19ba800bf3462d5ebd3f7c7210271f


---
# <a name="get-started-with-the-azure-iot-gateway-sdk-windows"></a>Introdução ao SDK do Gateway IoT do Azure (Windows)
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar a amostra
Antes de começar, é necessário [configurar seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Windows.

1. Abra um prompt de comando **Prompt de Comando do Desenvolvedor para VS2015** .
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Execute o scpript **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio e compila a solução. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **azure-iot-gateway-sdk**. Parâmetros adicionais podem ser fornecidos ao script para criar e executar testes de unidade e ponta a ponta. Esses parâmetros são **--run-unittests** e **--run-e2e-tests** respectivamente. 

## <a name="how-to-run-the-sample"></a>Como executar a amostra
1. O script **build.cmd** cria uma pasta chamada **build** na cópia local do repositório. Esta pasta contém os dois módulos usados nesta amostra.
   
    O script de build coloca **logger.dll** na pasta **build\\modules\\logger\\Debug** e **hello_world.dll** na pasta **build\\modules\\hello_world\\Debug**. Use esses caminhos para o valor de **caminho do módulo** , conforme mostrado no arquivo de configurações do JSON a seguir.
2. O processo de hello_world_sample leva o caminho até um arquivo de configuração JSON como um argumento na linha de comando. O exemplo de arquivo JSON a seguir foi fornecido como parte do repositório em **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json**. Ele funciona da forma como está, a menos que você tenha modificado o script de compilação e colocado módulos ou exemplo de executáveis em locais não padrão. 

   > [!NOTE]
   > Os caminhos de módulo são relativos ao diretório onde se encontra o hello_world_sample.exe. O arquivo de configuração do JSON de exemplo assume o padrão de gravar 'log.txt' no diretório de trabalho atual.
   
    ```
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
3. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk**.

4. Execute o comando a seguir:
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Jan17_HO3-->


