---
title: "Conectar o Arduino (C) ao IoT do Azure – Lição 4: nuvem para dispositivo | Microsoft Docs"
description: "Um aplicativo de exemplo é executado no Adafruit Feather M0 WiFi e monitora mensagens de entrada de seu Hub IoT. Uma nova tarefa gulp envia mensagens para seu Adafruit Feather M0 WiFi de seu Hub IoT para piscar o LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: controlar led pela web com arduino, controlar led via web com arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 63113841ca836681232e0aa43b15b444f8bb92e2
ms.contentlocale: pt-br
ms.lasthandoff: 01/28/2017

---
<a id="run-a-sample-application-to-receive-cloud-to-device-messages" class="xliff"></a>

# Executar um aplicativo de exemplo para receber mensagens da nuvem para o dispositivo
Neste artigo, você implanta um aplicativo de exemplo na placa Adafruit difusão M0 WiFi Arduino.

O aplicativo de exemplo monitora mensagens recebidas do hub IoT. Você também executa uma tarefa gulp no computador para enviar mensagens para a placa Arduino de seu hub IoT. Quando o aplicativo de exemplo recebe uma a mensagem, ele pisca o LED. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

<a id="what-you-will-do" class="xliff"></a>

## O que você fará
* Conectar o aplicativo de exemplo ao hub IoT.
* Implantar e executar o aplicativo de exemplo.
* Envie mensagens do hub IoT para a placa Arduino para piscar o LED.

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:
* Como monitorar mensagens recebidas do hub IoT.
* Agora, você pode enviar mensagens da nuvem para o dispositivo de seu Hub IoT para a placa Arduino.

<a id="what-you-need" class="xliff"></a>

## O que você precisa
* Sua placa Arduino, configurada para uso. Para saber como configurar a placa Arduino Pi, consulte [Configurar seu dispositivo][configure-your-device].
* Um hub IoT criado em sua assinatura do Azure. Para aprender a criar seu Hub IoT, consulte [Criar seu Hub IoT do Azure][create-your-azure-iot-hub].

<a id="connect-the-sample-application-to-your-iot-hub" class="xliff"></a>

## Conectar o aplicativo de exemplo ao Hub IoT

1. Verifique se você está na pasta de repositório `iot-hub-c-feather-m0-getting-started`.

   Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd Lesson4
   code .
   ```

   Observe o arquivo `app.ino` na subpasta `app`. O arquivo `app.ino` é o arquivo de origem principal que contém o código para monitorar mensagens recebidas do Hub IoT. A função `blinkLED` pisca o LED.

   ![Estrutura do repositório no aplicativo de exemplo][repo-structure]

2. Obter a porta serial do dispositivo com a CLI de descoberta de dispositivo:

   ```bash
   devdisco list --usb
   ```

   Você deve ver uma saída semelhante à seguinte e encontrar a porta COM USB para sua placa Arduino:

   ![Descoberta de dispositivo][device-discovery]

3. Abra o arquivo `config.json` na pasta da lição e adicione o valor do número da porta COM encontrado:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Para a porta COM, na plataforma Windows, ele tem o formato de `COM1, COM2, ...`. No macOS ou Ubuntu, começará com `/dev/`.

4. Inicialize o arquivo de configuração executando os seguintes comandos:

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. Faça as seguintes substituições no arquivo `config-arduino.json`:

   Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de funções do Azure][create-an-azure-function-app-and-storage-account] neste computador, todas as configurações serão herdadas, portanto você poderá ignorar a tarefa de implantar e executar o aplicativo de exemplo. Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de funções do Azure][create-an-azure-function-app-and-storage-account] em um computador diferente, será necessário substituir os espaços reservados no arquivo `config-arduino.json`. O arquivo `config-arduino.json` está na subpasta da pasta base.

   ![Conteúdo do arquivo config-arduino.json][config-arduino-json]

   * Substitua **[SSID Wi-Fi]** por seu SSID Wi-Fi conectado à Internet.
   * Substitua **[senha Wi-Fi]** pela senha de Wi-Fi. Remova a cadeia de caracteres se o Wi-Fi não precisar de uma senha.
   * Substitua **[cadeia de conexão do dispositivo IoT]** pela cadeia de conexão do dispositivo que você obtém executando o comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Substitua **[cadeia de conexão do hub IoT]** pela cadeia de conexão do hub IoT que você obtém executando o comando `az iot hub show-connection-string --name {my hub name}`.

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo na sua placa do Arduino executando os seguintes comandos:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

O comando gulp implanta o aplicativo de exemplo na sua placa Arduino. Por fim, ele executa o aplicativo na sua placa Arduino e uma tarefa separada no computador host para enviar 20 mensagens de piscar para sua placa Arduino do hub IoT.

Após a execução, o aplicativo de exemplo começa a escutar as mensagens do Hub IoT. Enquanto isso, a tarefa gulp envia várias mensagens de "piscar" do hub IoT para a sua placa Arduino. Para cada mensagem de piscar que a placa recebe, o aplicativo de exemplo chama a função `blinkLED` para piscar o LED.

Você deve ver o LED piscar a cada dois segundos, uma vez que a tarefa gulp está enviando 20 mensagens do Hub IoT para sua placa Arduino. A última é uma mensagem de “parar” que interrompe a execução do aplicativo.

![Exemplo de aplicativo com o comando gulp e mensagens de piscar][sample-application]

<a id="summary" class="xliff"></a>

## Resumo
Você enviou com êxito mensagens do hub IoT para sua placa Arduino para piscar o LED. A próxima tarefa é opcional: alterar o comportamento de liga e desliga do LED.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Alterar o comportamento de ligar e desligar do LED][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
