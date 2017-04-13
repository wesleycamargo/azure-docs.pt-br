---
title: "Conectar o Arduino (C) ao IoT do Azure - Lição 3: armazenamento de tabelas | Microsoft Docs"
description: "Monitore as mensagens do dispositivo para a nuvem conforme elas são gravadas no armazenamento de tabelas do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "dados na nuvem, coleta de dados de nuvem, serviço de nuvem iot, dados iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 386083e0-0dbb-48c0-9ac2-4f8fb4590772
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 7a49a15b00bf25c9416235f41da8cc86e54d0062
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens mantidas no Armazenamento do Azure
## <a name="what-you-will-do"></a>O que você fará
Monitore as mensagens do dispositivo para a nuvem enviadas de sua placa Adafruit Feather M0 WiFi Arduino para o Hub IoT conforme elas são gravadas no Armazenamento de Tabelas do Azure.

Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá como usar a tarefa read-message do gulp para ler mensagens mantidas no armazenamento de tabelas do Azure.

## <a name="what-you-need"></a>O que você precisa
Antes de iniciar esse processo, você precisa ter concluído com sucesso [Executar o aplicativo de exemplo de piscar do Azure em sua placa Arduino][run-blink-application].

## <a name="read-new-messages-from-your-storage-account"></a>Ler novas mensagens de sua conta de armazenamento
No artigo anterior, você executou um aplicativo de exemplo em sua placa Arduino. O aplicativo de exemplo envia mensagens para o hub IoT do Azure. As mensagens enviadas para o Hub IoT são armazenadas em seu armazenamento de tabelas do Azure por meio do aplicativo de funções do Azure. Você precisa da cadeia de conexão do Armazenamento do Azure para ler mensagens de seu armazenamento de tabelas do Azure.

Para ler as mensagens armazenadas em seu armazenamento de tabelas do Azure, siga estas etapas:

1. Obtenha a cadeia de conexão executando os seguintes comandos:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   O primeiro comando recupera o `storage name` usado no segundo comando para obter a cadeia de conexão. Use `iot-sample` como o valor de `{resource group name}` se não tiver alterado o valor.
2. Abra o arquivo de configuração `config-arduino.json` no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```
3. Substitua `[Azure storage connection string]` pela cadeia de conexão obtida na etapa 1.
4. Salve o arquivo `config-arduino.json`.
5. Envie mensagens novamente e leia-as de seu armazenamento de tabelas do Azure executando o seguinte comando:

   ```bash
   gulp run --read-storage

   # You can monitor the serial port by running listen task:
   gulp listen

   # Or you can combine above two gulp tasks into one:
   gulp run --read-storage --listen
   ```

   A lógica para leitura do armazenamento de tabelas do Azure está no arquivo `azure-table.js`.

   ![gulp run --read-storage][gulp-run]

## <a name="summary"></a>Resumo
Você conectou com sucesso sua placa Arduino ao Hub IoT na nuvem e usou o aplicativo de exemplo de piscar para enviar mensagens do dispositivo para a nuvem. Você também usou o aplicativo de funções do Azure para armazenar mensagens do hub IoT recebidas em seu armazenamento de tabelas do Azure. Agora, você pode enviar mensagens da nuvem para o dispositivo do Hub IoT para a placa Arduino.

## <a name="next-steps"></a>Próximas etapas
[Enviar mensagens da nuvem para o dispositivo][send-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[run-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[gulp-run]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_read_message_arduino.png
[send-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
