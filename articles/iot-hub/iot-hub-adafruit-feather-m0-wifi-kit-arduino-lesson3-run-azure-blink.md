---
title: "Conectar o Arduino (C) ao IoT do Azure - Lição 3: executar exemplo | Microsoft Docs"
description: Implante e execute um aplicativo de exemplo no Adafruit Feather M0 WiFi que envia mensagens ao Hub IoT e pisca o LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "serviço de nuvem iot, enviar dados para nuvem arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Executar um aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem
## <a name="what-you-will-do"></a>O que você fará
Este artigo mostrará como implantar e executar um aplicativo de exemplo em sua plava Adafruit Feather M0 WiFi Arduino que envia mensagens ao Hub IoT.

Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-you-will-learn"></a>O que você aprenderá
Você aprenderá a usar a ferramenta gulp para implantar e executar o aplicativo Arduino de exemplo em sua placa Arduino.

## <a name="what-you-need"></a>O que você precisa
* Antes de iniciar essa tarefa, você precisa ter concluído com sucesso [Criar um aplicativo de funções e uma conta de armazenamento do Azure para processar e armazenar mensagens do Hub IoT][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obter as cadeias de conexão do dispositivo e do Hub IoT
A cadeia de conexão do dispositivo é usada para conectar a placa Arduino ao Hub IoT. A cadeia de conexão do Hub IoT é usada para conectar o Hub IoT à identidade de dispositivo que representa a placa Arduino no Hub IoT.

* Liste todos os Hubs IoT no seu grupo de recursos executando o seguinte comando da CLI do Azure:

```bash
az iot hub list -g iot-sample --query [].name
```

Use `iot-sample` como o valor de `{resource group name}` se não tiver alterado o valor.

* Obtenha a cadeia de conexão do hub IoT executando o seguinte comando da CLI do Azure:

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` é o nome que você especificou quando criou o Hub IoT e registrou a placa Arduino.

* Obtenha a cadeia de conexão do dispositivo executando o seguinte comando:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

Use `mym0wifi` como o valor de `{device id}` se não tiver alterado o valor.
## <a name="configure-the-device-connection"></a>Configurar a conexão do dispositivo
Para configurar a conexão do dispositivo, siga estas etapas:

1. Obter a porta serial do dispositivo com a CLI de descoberta de dispositivo:

   ```bash
   devdisco list --usb
   ```

   Você deve ver uma saída semelhante à seguinte e encontrar a porta COM USB para sua placa Arduino:

   ![Descoberta de dispositivo][device-discovery]

2. Abra o arquivo `config.json` na pasta da lição e adicione o valor do número da porta COM encontrado:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Para a porta COM, na plataforma Windows, ele tem o formato de `COM1, COM2, ...`. No macOS ou Ubuntu, começa com `/dev/`.

3. Inicialize o arquivo de configuração executando os seguintes comandos:

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. Abra o arquivo de configuração `config-arduino.json` do dispositivo no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. Faça as seguintes substituições no arquivo `config-arduino.json`:

   * Substitua **[SSID Wi-Fi]** por seu SSID Wi-Fi conectado à Internet.
   * Substitua **[senha Wi-Fi]** pela senha de Wi-Fi. Remova a cadeia de caracteres se o Wi-Fi não precisar de uma senha.
   * Substitua **[cadeia de conexão do dispositivo IoT]** pelo `device connection string` que você obteve.
   * Substitua **[cadeia de conexão do hub IoT]** pelo `iot hub connection string` que você obteve.

   > [!NOTE]
   > Você não precisa do `azure_storage_connection_string` neste artigo. Mantenha como está.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo na sua placa do Arduino executando o seguinte comando:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> A tarefa de gulp padrão executa as tarefas `install-tools` e `run` em sequência. Quando [implantou o aplicativo de piscar][deployed-the-blink-app], você executou estas tarefas separadamente.

## <a name="verify-that-the-sample-application-works"></a>Verificar se o aplicativo de exemplo funciona
Você deve ver o LED do GPIO núm. 0 na placa piscando a cada dois segundos. Sempre que o LED pisca, o aplicativo de exemplo envia uma mensagem ao hub IoT e verifica se a mensagem foi enviada com êxito para o hub IoT. Além disso, cada mensagem recebida pelo Hub IoT é impressa na janela do console. O aplicativo de exemplo é encerrado automaticamente após o envio de 20 mensagens.

![Exemplo de aplicativo com mensagens enviadas e recebidas][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Resumo
Você implantou e executou o novo aplicativo de exemplo de piscar em sua placa Arduino para enviar mensagens do dispositivo para a nuvem para o hub IoT. Agora, você monitorara suas mensagens conforme elas são gravadas na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas
[Ler mensagens persistentes no Armazenamento do Azure][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
