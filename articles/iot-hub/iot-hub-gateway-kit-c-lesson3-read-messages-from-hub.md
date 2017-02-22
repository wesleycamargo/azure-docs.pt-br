---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Lição 3: ler mensagens | Microsoft Docs"
description: "Execute um código de exemplo no computador host para ler as mensagens de seu Hub IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "dados na nuvem, coleta de dados de nuvem, serviço de nuvem iot, dados iot"
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 0a5dd7e010ba46b724bb6a2526dc2a2730acc10f


---

# <a name="read-messages-from-your-iot-hub"></a>Ler mensagens de seu Hub IoT

## <a name="what-you-will-do"></a>O que você fará

- Execute um código de exemplo no computador host para ler mensagens de seu Hub IoT.

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Como usar a ferramenta gulp para ler mensagens de seu Hub IoT.

## <a name="what-you-need"></a>O que você precisa

- O aplicativo de exemplo BLE que você executou com êxito na Lição 3.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obter as cadeias de conexão do dispositivo e do Hub IoT

Essa cadeia de conexão é usada pelo seu dispositivo (SensorTag de TI ou dispositivo simulado) para se conectar ao seu Hub IoT como um dispositivo. A cadeia de conexão do Hub IoT é usada para se conectar ao Registro de identidade em seu Hub IoT para gerenciar os dispositivos que têm permissão para se conectar ao seu Hub IoT.

- Listar todos os Hubs IoT no grupo de recursos executando o seguinte comando:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Use `iot-gateway` como o valor de `{resource group name}` se não tiver alterado o valor.
- Obtenha a cadeia de conexão do Hub IoT executando o seguinte comando:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` é o nome que você especificou na Lição 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configurar a conexão do dispositivo para o código de exemplo

Atualize o arquivo de configuração do dispositivo `config-azure.json` para que você possa ler mensagens de seu Hub IoT no computador host. Para fazer isso, siga estas etapas:

1. Abra `config-azure.json` no Visual Studio Code executando o seguinte comando em uma janela do console:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Faça as seguintes substituições no arquivo `config-azure.json`:

   ![instantâneo de configuração do Azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Substitua `[IoT hub connection string]` pela cadeia de conexão do Hub IoT obtida.

## <a name="read-messages-from-your-iot-hub"></a>Ler mensagens de seu Hub IoT

Se você tiver uma SensorTag de TI, certifique-se de já ter ligado a SensorTag. Execute o aplicativo de exemplo no gateway e leia as mensagens do Hub IoT por meio do seguinte comando:

```bash
gulp run --iot-hub
```

O comando executa o aplicativo de exemplo BLE, que lê e empacota dados de temperatura do dispositivo simulado ou SensorTag e envia a mensagem para o Hub IoT a cada 2 segundos. Ele também gera um processo filho para receber a mensagem.

As mensagens que estão sendo enviadas e recebidas são todas exibidas instantaneamente na mesma janela de console no computador host. A instância do aplicativo de exemplo será encerrada automaticamente em 40 segundos.

![Aplicativo de exemplo BLE com mensagens enviadas e recebidas](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>Resumo

Você executou um código de exemplo para ler mensagens de seu Hub IoT. Você está pronto para ler as mensagens que estão armazenadas no seu Armazenamento de Tabelas do Azure.

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Jan17_HO4-->


