---
title: "Conectar Raspberry Pi (C) ao IoT do Azure – Lição 4: nuvem para o dispositivo | Microsoft Docs"
description: "Um aplicativo de exemplo é executado em seu Pi e monitora mensagens de entrada de seu Hub IoT. Uma nova tarefa gulp envia mensagens para seu Pi de seu Hub IoT para piscar o LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: nuvem para o dispositivo, mensagem da nuvem
ms.assetid: fcbc0dd0-cae3-47b0-8e58-240e4f406f75
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 3a6957ebbb239eacb4fce696b7fcfd02e690310b


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Executar um aplicativo de exemplo para receber mensagens da nuvem para o dispositivo
Neste artigo, implante um aplicativo de exemplo no Raspberry Pi 3. O aplicativo de exemplo monitora mensagens recebidas do hub IoT. Você também executa uma tarefa gulp no computador para enviar mensagens para o Pi do hub IoT. Quando o aplicativo de exemplo recebe uma a mensagem, ele pisca o LED. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-do"></a>O que você fará
* Conectar o aplicativo de exemplo ao hub IoT.
* Implantar e executar o aplicativo de exemplo.
* Enviar mensagens do hub IoT para o Pi para piscar o LED.

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como monitorar mensagens recebidas do hub IoT.
* Como enviar mensagens de nuvem para dispositivo do hub IoT para o Pi.

## <a name="what-you-need"></a>O que você precisa
* Raspberry Pi 3, configuração para uso. Para saber como configurar o Pi, consulte [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md).
* Um hub IoT criado em sua assinatura do Azure. Para saber como criar seu Hub IoT, consulte [Criar seu Hub IoT e registrar o Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Conectar o aplicativo de exemplo ao Hub IoT
1. Verifique se você está na pasta de repositório `iot-hub-c-raspberrypi-getting-started`. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd Lesson4
   code .
   ```

   Observe o arquivo `app.c` na subpasta `app`. O arquivo `app.c` é o arquivo de origem principal que contém o código para monitorar mensagens recebidas do Hub IoT. A função `blinkLED` pisca o LED.

   ![Estrutura do repositório no aplicativo de exemplo](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure_c.png)
2. Inicialize o arquivo de configuração executando os seguintes comandos:

   ```bash
   npm install
   gulp init
   ```

   Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de funções do Azure](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md) neste computador, todas as configurações serão herdadas, portanto você pode pular para a etapa da tarefa de implantar e executar o aplicativo de exemplo. Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de função do Azure](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md) em um computador diferente, será necessário substituir os espaços reservados no arquivo `config-raspberrypi.json`. O arquivo `config-raspberrypi.json` está na subpasta da pasta base.

   ![Conteúdo do arquivo config-raspberrypi.json](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* Substitua **[nome de host ou endereço IP do dispositivo]** pelo nome de host ou o endereço IP do Pi obtido executando o comando `devdisco list --eth`.
* Substitua **[cadeia de conexão do dispositivo IoT]** pela cadeia de conexão do dispositivo que você obtém executando o comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}`.
* Substitua **[cadeia de conexão do hub IoT]** pela cadeia de conexão do hub IoT que você obtém executando o comando `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}`.

> [!NOTE]
> Execute **gulp install-tools** e também, se você ainda não fez isso na Lição 1.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo no Pi executando os seguintes comandos:

```
gulp deploy && gulp run
```

O comando gulp executa a tarefa install-tools primeiro. Em seguida, ele implanta o aplicativo de exemplo para o Pi. Por fim, ele executa o aplicativo no Pi e uma tarefa separada no computador host para enviar 20 mensagens de piscar para o Pi do Hub IoT.

Após a execução, o aplicativo de exemplo começa a escutar as mensagens do Hub IoT. Enquanto isso, a tarefa gulp envia várias mensagens de "piscar" do Hub IoT para o Pi. Para cada mensagem de piscar recebida, o aplicativo de exemplo chama a função `blinkLED` para piscar o LED.

Você deve ver o LED piscar a cada dois segundos, uma vez que a tarefa gulp está enviando 20 mensagens do Hub IoT ao Pi. A última é uma mensagem de “parar” que interrompe a execução do aplicativo.

![Exemplo de aplicativo com o comando gulp e mensagens de piscar](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink_c.png)

## <a name="summary"></a>Resumo
Você enviou com êxito mensagens do Hub IoT para o Pi para piscar o LED. A próxima tarefa é opcional: alterar o comportamento de liga e desliga do LED.

## <a name="next-steps"></a>Próximas etapas
[Alterar o comportamento de ligar e desligar do LED](iot-hub-raspberry-pi-kit-c-lesson4-change-led-behavior.md)



<!--HONumber=Jan17_HO4-->


