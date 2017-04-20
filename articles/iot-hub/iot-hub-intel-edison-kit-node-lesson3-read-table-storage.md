---
title: "Conectar o Intel Edison (Nó) ao IoT do Azure - Lição 3: monitorar mensagens| Microsoft Docs"
description: "Monitore as mensagens do dispositivo para a nuvem conforme elas são gravadas no armazenamento de tabelas do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "dados na nuvem, coleta de dados de nuvem, serviço de nuvem iot, dados iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: fa2c7efe-7e34-4e39-bb70-015c15ac69ed
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 5100e6dbd74095f419c1017a3784fac547bf200b
ms.lasthandoff: 01/25/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens mantidas no Armazenamento do Azure
## <a name="what-you-will-do"></a>O que você fará
Monitorar as mensagens do dispositivo para a nuvem enviadas do Intel Edison para o Hub IoT conforme elas são gravadas no Armazenamento de Tabelas do Azure. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá como usar a tarefa read-message do gulp para ler mensagens mantidas no armazenamento de tabelas do Azure.

## <a name="what-you-need"></a>O que você precisa
Antes de iniciar esse processo, você precisa ter concluído com sucesso [Executar o aplicativo de exemplo de piscar do Azure no Intel Edison][run-the-azure-blink-sample-application-on-intel-edison].

## <a name="read-new-messages-from-your-storage-account"></a>Ler novas mensagens de sua conta de armazenamento
No artigo anterior, você executou um aplicativo de exemplo no Edison. O aplicativo de exemplo envia mensagens para o hub IoT do Azure. As mensagens enviadas para o Hub IoT são armazenadas em seu armazenamento de tabelas do Azure por meio do aplicativo de funções do Azure. Você precisa da cadeia de conexão do Armazenamento do Azure para ler mensagens de seu armazenamento de tabelas do Azure.

Para ler as mensagens armazenadas em seu armazenamento de tabelas do Azure, siga estas etapas:

1. Obtenha a cadeia de conexão executando os seguintes comandos:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   O primeiro comando recupera o `storage name` usado no segundo comando para obter a cadeia de conexão. Use `iot-sample` como o valor de `{resource group name}` se não tiver alterado o valor.
2. Abra o arquivo de configuração `config-edison.json` no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. Substitua `[Azure storage connection string]` pela cadeia de conexão obtida na etapa 1.
4. Salve o arquivo `config-edison.json`.
5. Envie mensagens novamente e leia-as de seu armazenamento de tabelas do Azure executando o seguinte comando:

   ```bash
   gulp run --read-storage
   ```

   A lógica para leitura do armazenamento de tabelas do Azure está no arquivo `azure-table.js`.

   ![gulp run --read-storage][gulp run]

## <a name="summary"></a>Resumo
Você conectou com sucesso o Edison ao Hub IoT na nuvem e usou o aplicativo de exemplo de piscar para enviar mensagens do dispositivo para a nuvem. Você também usou o aplicativo de funções do Azure para armazenar mensagens do hub IoT recebidas em seu armazenamento de tabelas do Azure. Agora, é possível enviar mensagens da nuvem para o dispositivo do Hub IoT para o Edison.

## <a name="next-steps"></a>Próximas etapas
[Executar um aplicativo de exemplo para receber mensagens da nuvem para o dispositivo][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
