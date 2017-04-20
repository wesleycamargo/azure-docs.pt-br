---
featureFlags:
- usabilla
title: "Conectar o Raspberry Pi (Nó) ao IoT do Azure - Lição 4: nuvem para o dispositivo | Microsoft Docs"
description: "O aplicativo de exemplo é executado em seu Pi e monitora mensagens de entrada de seu Hub IoT. Uma nova tarefa gulp envia mensagens para seu Pi de seu Hub IoT para piscar o LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: nuvem para o dispositivo, mensagem da nuvem
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 6ae6539e-1163-4490-8d72-fdf7803e3054
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 92ee9d6faae9f539c663395e47714609a146f2df
ms.lasthandoff: 01/24/2017


---
# <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Executar o aplicativo de exemplo para receber mensagens da nuvem para o dispositivo
Neste artigo, implante um aplicativo de exemplo no Raspberry Pi 3. O aplicativo de exemplo monitora mensagens recebidas do hub IoT. Você também executa uma tarefa gulp no computador para enviar mensagens para o Pi do hub IoT. Quando o aplicativo de exemplo recebe uma a mensagem, ele pisca o LED. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-do"></a>O que você fará
* Conectar o aplicativo de exemplo ao hub IoT.
* Implantar e executar o aplicativo de exemplo.
* Enviar mensagens do hub IoT para o Pi para piscar o LED.

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como monitorar mensagens recebidas do Hub IoT
* Como enviar mensagens de nuvem para dispositivo do hub IoT para o Pi.

## <a name="what-you-need"></a>O que você precisa
* Raspberry Pi 3, configuração para uso. Para saber como configurar o Pi, consulte [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md).
* Um hub IoT criado em sua assinatura do Azure. Para saber como criar seu Hub IoT, consulte [Criar seu Hub IoT e registrar o Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Conectar o aplicativo de exemplo ao Hub IoT
1. Verifique se você está na pasta de repositório `iot-hub-node-raspberrypi-getting-started`. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:
   
   ```bash
   cd Lesson4
   code .
   ```
   
   Observe o arquivo `app.js` na subpasta `app`. O arquivo `app.js` é o arquivo de origem principal que contém o código para monitorar mensagens recebidas do Hub IoT. A função `blinkLED` pisca o LED.
   
   ![Estrutura do repositório no aplicativo de exemplo](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)
2. Inicialize o arquivo de configuração usando os seguintes comandos:
   
   ```bash
   npm install
   gulp init
   ```
   
   Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de função do Azure](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) neste computador, todas as configurações serão herdadas, portanto você pode ignorar a tarefa de implantar e executar o aplicativo de exemplo. Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de função do Azure](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) em um computador diferente, será necessário substituir os espaços reservados no arquivo `config-raspberrypi.json`. O arquivo `config-raspberrypi.json` está na subpasta da pasta base.
   
   ![Conteúdo do arquivo config-raspberrypi.json](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* Substitua **[nome de host ou endereço IP do dispositivo]** pelo endereço IP do Pi ou o nome de host que você obtém executando o comando `devdisco list --eth`.
* Substitua **[cadeia de conexão do dispositivo IoT]** pela cadeia de conexão do dispositivo que você obtém executando o comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}`.
* Substitua **[cadeia de conexão do hub IoT]** pela cadeia de conexão do hub IoT que você obtém executando o comando `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}`.

> [!NOTE]
> Execute **gulp install-tools** e também, se você ainda não fez isso na Lição 1.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo no Pi executando o seguinte comando:

```bash
gulp deploy && gulp run
```

O comando implanta o aplicativo de exemplo para o Pi. Em seguida, ele executa o aplicativo no Pi e uma tarefa separada no computador host para enviar 20 mensagens de piscar para o Pi do hub IoT.

Após a execução, o aplicativo de exemplo começa a escutar as mensagens do Hub IoT. Enquanto isso, a tarefa gulp envia várias mensagens de "piscar" do Hub IoT para o Pi. Para cada mensagem de piscar recebida, o aplicativo de exemplo chama a função `blinkLED` para piscar o LED.

Você deve ver o LED piscar a cada dois segundos, uma vez que a tarefa gulp está enviando 20 mensagens do Hub IoT ao Pi. A última é uma mensagem de “parar” que diz para o aplicativo interromper a execução.

![Exemplo de aplicativo com o comando gulp e mensagens de piscar](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="summary"></a>Resumo
Você enviou com êxito mensagens do Hub IoT para o Pi para piscar o LED. A próxima tarefa é opcional: alterar o comportamento de liga e desliga do LED.

## <a name="next-steps"></a>Próximas etapas
[Alterar o comportamento de ligar e desligar do LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


