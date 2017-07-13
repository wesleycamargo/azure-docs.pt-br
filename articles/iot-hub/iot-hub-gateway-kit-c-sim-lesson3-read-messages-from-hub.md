---
title: "Dispositivo simulado e Gateway do IoT do Azure - Lição 3: ler mensagens | Microsoft Docs"
description: "Execute um código de exemplo no computador host para ler as mensagens de seu Hub IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "dados na nuvem, coleta de dados de nuvem, serviço de nuvem iot, dados iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6
ms.contentlocale: pt-br
ms.lasthandoff: 01/25/2017

---

<a id="read-messages-from-your-iot-hub" class="xliff"></a>

# Ler mensagens de seu Hub IoT

<a id="what-you-will-do" class="xliff"></a>

## O que você fará

- Execute um código de exemplo no computador host para ler mensagens de seu Hub IoT.

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá

Como usar a ferramenta gulp para ler mensagens de seu Hub IoT.

<a id="what-you-need" class="xliff"></a>

## O que você precisa

- O exemplo de dispositivo simulado em [Configurar e executar um aplicativo de exemplo de upload de nuvem de dispositivo simulado](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

<a id="get-your-iot-hub-and-device-connection-strings" class="xliff"></a>

## Obter as cadeias de conexão do dispositivo e do Hub IoT

A cadeia de conexão do dispositivo é usada pelo seu dispositivo simulado para conectar ao seu Hub IoT. A cadeia de conexão do Hub IoT é usada para se conectar ao Registro de identidade em seu Hub IoT para gerenciar os dispositivos que têm permissão para se conectar ao seu Hub IoT.

- Listar todos os Hubs IoT no grupo de recursos executando o seguinte comando:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Use `iot-gateway` como o valor de `{resource group name}` se você não o tiver alterado.
- Obtenha a cadeia de conexão do Hub IoT executando o seguinte comando:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` é o nome que você especificou na Lição 2.

<a id="configure-the-device-connection-for-the-sample-code" class="xliff"></a>

## Configurar a conexão do dispositivo para o código de exemplo

Atualizar configurações de Hub IoT e de conexão do dispositivo em `config-azure.json` executando as seguintes etapas:

1. Abra `config-azure.json` no Visual Studio Code executando o seguinte comando em uma janela do console:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Faça as seguintes substituições no arquivo `config-azure.json`:

   ![instantâneo de configuração do Azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Substitua `[IoT hub connection string]` pela cadeia de conexão do Hub IoT.

<a id="read-messages-from-your-iot-hub" class="xliff"></a>

## Ler mensagens de seu Hub IoT

Execute o aplicativo de exemplo de dispositivo simulado e leia as mensagens do Hub IoT por meio do seguinte comando:

```bash
gulp run --iot-hub
```

O comando executa o aplicativo que envia mensagens ao Hub IoT a cada dois segundos. Ele também gera um processo filho para receber a mensagem.

As mensagens que estão sendo enviadas e recebidas são todas exibidas instantaneamente na mesma janela de console no computador host. O aplicativo será encerrado em 40 segundos.

![O aplicativo de exemplo simulado com mensagens enviadas e recebidas](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

<a id="summary" class="xliff"></a>

## Resumo

Você executou com êxito o aplicativo de exemplo para enviar dados para o Hub IoT com o dispositivo simulado. Você também leu as mensagens que foram enviadas para o Hub IoT.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)



