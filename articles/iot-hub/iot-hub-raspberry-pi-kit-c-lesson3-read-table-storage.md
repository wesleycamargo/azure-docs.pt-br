---
title: "Conectar o Raspberry Pi (Nó) ao IoT do Azure - Lição 3: armazenamento de tabelas | Microsoft Docs"
description: "Monitore as mensagens do dispositivo para a nuvem conforme elas são gravadas no armazenamento de tabelas do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "recuperar dados da nuvem, serviço de nuvem de iot"
ms.assetid: 8c5558bb-3c31-4445-90e6-b1a978738545
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41e89f524bf9ae67489ab3a0eb09ceda6b211f15
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens mantidas no Armazenamento do Azure
## <a name="what-you-will-do"></a>O que você fará
Monitore as mensagens do dispositivo para a nuvem enviadas do Raspberry Pi 3 para o hub IoT conforme elas são gravadas no armazenamento de tabelas do Azure. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá como usar a tarefa read-message do gulp para ler mensagens mantidas no armazenamento de tabelas do Azure.

## <a name="what-you-need"></a>O que você precisa
Antes de iniciar esse processo você deve ter concluído com sucesso [Executar o aplicativo de exemplo de piscar do Azure em seu Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md).

## <a name="read-new-messages-from-your-storage-account"></a>Ler novas mensagens de sua conta de armazenamento
No artigo anterior, você executou um aplicativo de exemplo no Pi. O aplicativo de exemplo envia mensagens para o hub IoT do Azure. As mensagens enviadas para o Hub IoT são armazenadas em seu armazenamento de tabelas do Azure por meio do aplicativo de funções do Azure. Você precisa da cadeia de conexão do Armazenamento do Azure para ler mensagens de seu armazenamento de tabelas do Azure.

Para ler as mensagens armazenadas em seu armazenamento de tabelas do Azure, siga estas etapas:

1. Obtenha a cadeia de conexão executando os seguintes comandos:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   O primeiro comando recupera o `storage name` usado no segundo comando para obter a cadeia de conexão. Use `iot-sample` como o valor de `{resource group name}` se não tiver alterado o valor.
2. Abra o arquivo de configuração `config-raspberrypi.json` no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. Substitua `[Azure storage connection string]` pela cadeia de conexão obtida na etapa 1.
4. Salve o arquivo `config-raspberrypi.json`.
5. Envie mensagens novamente e leia-as de seu armazenamento de tabelas do Azure executando o seguinte comando:
   
   ```bash
   gulp run --read-storage
   ```
   
   A lógica para leitura do armazenamento de tabelas do Azure está no arquivo `azure-table.js`.
   
   ![gulp run --read-storage](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message_c.png)

## <a name="summary"></a>Resumo
Você conectou com sucesso o Pi ao Hub IoT na nuvem e usou o aplicativo de exemplo de piscar para enviar mensagens do dispositivo para a nuvem. Você também usou o aplicativo de funções do Azure para armazenar mensagens do hub IoT recebidas em seu armazenamento de tabelas do Azure. Agora você pode enviar mensagens de nuvem para dispositivo do Hub IoT para o Pi.

## <a name="next-steps"></a>Próximas etapas
[Executar um aplicativo de exemplo para receber mensagens da nuvem para o dispositivo](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)


