---
title: Executar o aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem | Microsoft Docs
description: Implante e execute um aplicativo de exemplo para o Raspberry Pi 3 que envie mensagens ao seu Hub IoT e pisque o LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: pi de nuvem do led de piscar, led de piscar da nuvem
ms.assetid: 427d8e5e-8af8-4249-8607-44edc90a4972
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: 0c60200f87bf2c1df0a32b1887b1f9412ba69b39


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Executar um aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem
## <a name="what-you-will-do"></a>O que você fará
Esse artigo mostrará como implantar e executar um aplicativo de exemplo para seu Raspberry Pi 3 que envie mensagens ao Hub IoT. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Você aprenderá como usar a ferramenta gulp para implantar e executar o aplicativo de exemplo Node.js no Pi.

## <a name="what-you-need"></a>O que você precisa
Antes de iniciar essa tarefa, você deve ter concluído com sucesso [Criar um aplicativo de funções do Azure e uma conta de armazenamento para processar e armazenar mensagens do Hub IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obter as cadeias de conexão do dispositivo e do Hub IoT
A cadeia de conexão do dispositivo é usada para conectar o Pi ao Hub IoT. A cadeia de conexão do Hub IoT é usada para conectar o Hub IoT à identidade do dispositivo que representa o Pi no Hub IoT.

* Obtenha a cadeia de conexão do hub IoT executando o seguinte comando da CLI do Azure:

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}` é o nome que você especificou quando criou o Hub IoT e registrou o Pi. Use `iot-sample` como o valor de `{resource group name}` se não tiver alterado o valor.

* Obtenha a cadeia de conexão do dispositivo executando o seguinte comando:

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}` usa o mesmo valor usado com o comando anterior. Use `iot-sample` como o valor de `{resource group name}` e use `myraspberrypi` como o valor de `{device id}` se você não tiver alterado o valor.

## <a name="configure-the-device-connection"></a>Configurar a conexão do dispositivo
1. Inicialize o arquivo de configuração executando os seguintes comandos:
   
    ```bash
    npm install
    gulp init
    ```
2. Abra o arquivo de configuração `config-raspberrypi.json` do dispositivo no Visual Studio Code executando o seguinte comando:
   
    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```
   
    ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. Faça as seguintes substituições no arquivo `config-raspberrypi.json`:
   
   * Substitua **[nome do host ou endereço IP do dispositivo]** pelo endereço IP ou o nome de host do dispositivo que você obteve de `device-discovery-cli` ou pelo valor herdado quando você configurou o seu dispositivo.
   * Substitua **[cadeia de conexão do dispositivo IoT]** pelo `device connection string` que você obteve.
   * Substitua **[cadeia de conexão do hub IoT]** pelo `iot hub connection string` que você obteve.

Atualize o arquivo `config-raspberrypi.json` para que você possa implantar o aplicativo de exemplo de seu computador.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo no Pi executando o seguinte comando:

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>Verificar se o aplicativo de exemplo funciona
Você deve ver o LED que está conectado ao Pi piscar a cada dois segundos. Sempre que o LED pisca, o aplicativo de exemplo envia uma mensagem ao hub IoT e verifica se a mensagem foi enviada com êxito para o hub IoT. Além disso, cada mensagem recebida pelo Hub IoT é impressa na janela do console. O aplicativo de exemplo é encerrado automaticamente após o envio de 20 mensagens.

![Exemplo de aplicativo com mensagens enviadas e recebidas](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="summary"></a>Resumo
Você implantou e executou o novo aplicativo de exemplo de piscar no Pi para enviar mensagens do dispositivo para a nuvem para o Hub IoT. Você pode monitorar suas mensagens conforme elas são gravadas na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas
[Ler mensagens mantidas no Armazenamento do Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)




<!--HONumber=Nov16_HO5-->


