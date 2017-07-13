---
title: "Criar seu primeiro módulo Azure IoT Gateway | Microsoft Docs"
description: "Crie um módulo e adicioná-la a um aplicativo de exemplo para personalizar os comportamentos de módulo."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5e28422158684c3aaf0ac3fdf5b19c80fbccfb02
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017

---
<a id="lesson-5-create-your-first-azure-iot-gateway-module" class="xliff"></a>

# Lição 5: Criar o primeiro módulo de Gateway de IoT do Azure
Embora o Azure IoT Edge permita que você crie módulos escritos em Java, .NET ou Node.js, este tutorial explica as etapas para criar um módulo em C.

<a id="what-you-will-do" class="xliff"></a>

## O que você fará

- Compile e execute o aplicativo de exemplo hello_world no Intel NUC.
- Crie um módulo e o compile no Intel NUC.
- Adicione o novo módulo ao aplicativo de exemplo hello_world e então execute a amostra no Intel NUC. O novo módulo imprime mensagens de "hello_world" com um carimbo de hora.

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá

- Como compilar e executar um aplicativo de exemplo no Intel NUC.
- Como criar um módulo.
- Como adicionar o módulo para um aplicativo de exemplo.

<a id="what-you-need" class="xliff"></a>

## O que você precisa

O Azure IoT Edge foi instalado no computador host.

<a id="folder-structure" class="xliff"></a>

## Estrutura de pastas

Na subpasta Lesson 5 do código de exemplo que você clonou na lição 1, há uma pasta `module` e uma pasta `sample`.

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- A pasta `module/my_module` contém o código-fonte e o script para criar o módulo.
- A pasta `sample` contém o código-fonte e o script para criar o aplicativo de exemplo.

<a id="compile-and-run-the-helloworld-sample-app-on-intel-nuc" class="xliff"></a>

## Compile e execute o aplicativo de exemplo hello_world em Intel NUC

O exemplo `hello_world` cria um gateway com base no arquivo `hello_world.json` que especifica os dois módulos predefinidos associados ao aplicativo. O gateway registra uma mensagem "hello world" em um arquivo de 5 segundos. Nesta seção, você pode compilar e executar o aplicativo `hello_world` com seu módulo padrão.

Para compilar e executar o aplicativo `hello_world`, siga estas etapas no computador host:

1. Inicialize os arquivos de configuração executando os seguintes comandos:

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. Atualize o arquivo de configuração do gateway com o endereço MAC do Intel NUC. Ignore esta etapa se você visto a lição para [configurar e executar um aplicativo de exemplo BLE][config_ble].

   1. Abra o arquivo de configuração do gateway executando o seguinte comando:

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. Atualize o endereço MAC do gateway quando [configurar o Intel NUC como um gateway IoT][setup_nuc] e então salve o arquivo.

1. Compile o código-fonte, executando o seguinte comando:

   ```bash
   gulp compile
   ```

   O comando transfere o código-fonte para Intel NUC e executa `build.sh` para compilá-lo.

1. Execute o aplicativo `hello_world` no Intel NUC executando o seguinte comando:

   ```bash
   gulp run
   ```

   O comando executa `../Tools/run-hello-world.js`, que é especificado em `config.json` para iniciar o aplicativo de exemplo no Intel NUC.

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

<a id="create-a-new-module-and-compile-it-on-intel-nuc" class="xliff"></a>

## Crie um novo módulo e compilá-lo na Intel NUC

As etapas a seguir orientam você durante a criação de um novo módulo e compile-o no Intel NUC. O módulo imprime mensagens com um carimbo de hora após recebê-los. Você irá criar o primeiro módulo gateway personalizado nesta seção.

Qualquer módulo do Azure IoT Edge deve implementar as seguintes interfaces:

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

Opcionalmente, você pode implementar a interface a seguir:

   ```C
   pfModule_Start Module_Start
   ```

O diagrama a seguir mostra os caminhos de estado importantes de um módulo. Os retângulos quadrados representam métodos que implementar para executar operações quando o módulo se movem entre estados. Os ovais são principais estados que de módulo pode ser no.

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

Agora vamos criar um módulo com base no modelo:

1. Abra a pasta de modelos, executando o seguinte comando:

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` serve como um modelo que facilita a criação de um módulo. O modelo declara as interfaces. Tudo o que você precisa fazer é adicionar lógica à função `MyModule_Receive`.
   - `build.sh` é o script de compilação para compilar o módulo no Intel NUC.
1. Abra o `src/my_module.c` de arquivo e inclui dois arquivos de cabeçalho:

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. Adicione o seguinte código à função `MyModule_Receive`:

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. Atualize o arquivo `config.json` para especificar a pasta `workspace` no computador host e o caminho de implantação no Intel NUC. Durante a compilação, os arquivos na pasta `workspace` serão transferidos para o caminho de implantação.

   1. Abra o arquivo `config.json` executando o seguinte comando:

      ```bash
      code config.json
      ```

   1. Atualize `config.json` com a seguinte configuração:

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. Compile o módulo executando o seguinte comando:

   ```bash
   gulp compile
   ```

   O comando transfere o código-fonte para Intel NUC e executa `build.sh` para compilar o módulo.

<a id="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc" class="xliff"></a>

## Adicionar o módulo para o aplicativo de exemplo hello_world e execute o aplicativo em Intel NUC

Para executar essa tarefa, siga estas etapas:

1. Liste todos os módulo binários (arquivos .so) no Intel NUC executando o seguinte comando:

   ```bash
   gulp modules --list
   ```

   O caminho binário do `my_module` que você compilou deve estar listado como abaixo:

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   Se você alterar o nome de usuário de logon padrão em `config-gateway.json`, o caminho binário será iniciado com `home/<your username>` em vez de `root`.

1. Adicione `my_module` ao aplicativo e exemplo `hello_world`:

   1. Abra o arquivo `hello_world.json` executando o seguinte comando:

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. Adicione o seguinte código à seção `modules`:

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      O valor de `module.path` deve ser `/root/gateway_sample/module/my_module/build/libmy_module.so`. O código declara `my_module` a ser associado com o gateway, bem como o local do módulo binário especificado em `module.path`.
   1. Adicione o seguinte código à seção `links`:

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      Esse código especifica que as mensagens são transferidas do `hello_world` módulo `my_module`.

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. Execute o `hello_world` aplicativo de exemplo, executando o seguinte comando:

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   O `--config` parâmetro força o `run-hello-world.js` script a ser executado usando o `hello_world.json` arquivo.

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

Parabéns. Agora você pode ver o comportamento do novo módulo, ele simplesmente imprime mensagens de "hello world" com um carimbo de hora, um resultado diferente do módulo "hello_world" original.

<a id="next-steps" class="xliff"></a>

## Próximas etapas

Você já criou um novo módulo, adicionou à hello_world exemplo e obter o aplicativo de exemplo para executar com o novo módulo no gateway. Se você quiser saber mais sobre os módulos de gateway IoT do Azure, você pode encontrar mais exemplos de módulo: [https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules).

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

