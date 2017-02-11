---
title: "Implantar o aplicativo de piscar no Kit de início de IoT do Azure | Microsoft Docs"
description: Clone o aplicativo Arduino de exemplo do GitHub e execute o gulp para implantar esse aplicativo no Adafruit Feather M0 WiFi. Este aplicativo de exemplo pisca o GPIO
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "projetos led arduino, piscar led arduino, código piscar led arduino, programa de piscar arduino, exemplo de piscar arduino"
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 918c8d7c732dc9b50c1e22917d3c4314c326a57d


---
# <a name="create-and-deploy-the-blink-application"></a>Criar e implantar o aplicativo de piscar
## <a name="what-you-will-do"></a>O que você fará
Clonar o aplicativo Arduino de exemplo do GitHub e usar a ferramenta gulp para implantar esse aplicativo de exemplo na placa Adafruit Feather M0 WiFi Arduino. O aplicativo de exemplo pisca o LED conectado à placa no GPIO núm. 13 a cada dois segundos.

Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting-page].

## <a name="what-you-will-learn"></a>O que você aprenderá
* Como implantar e executar o aplicativo de exemplo em sua placa Arduino.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso as seções a seguir:

* [Configurar seu dispositivo][configure-your-device]
* [Obter as ferramentas][get-the-tools]

## <a name="open-the-sample-application"></a>Abrir o aplicativo de exemplo
Para abrir o aplicativo de exemplo, siga estas etapas:

1. Clone o repositório de exemplo do GitHub executando o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![Estrutura do repositório][repo-structure]

O arquivo `app.ino` na subpasta `app` é o arquivo de origem principal que contém o código para controlar o LED.

### <a name="install-application-dependencies"></a>Instalar dependências de aplicativos
Instale as bibliotecas e outros módulos necessários para o aplicativo de exemplo executando o seguinte comando:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configurar a conexão do dispositivo
Para configurar a conexão do dispositivo, siga estas etapas:

1. Obter a porta serial do dispositivo com a CLI de descoberta de dispositivo:

   ```bash
   devdisco list --usb
   ```

   Você deve ver uma saída semelhante à seguinte e encontrar a porta COM USB para sua placa Arduino: ![Descoberta de dispositivos][device-discovery]

2. Abra o arquivo `config.json` na pasta da lição e adicione o valor do número da porta COM encontrado:

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > Para a porta COM, na plataforma Windows, ele tem o formato de `COM1, COM2, ...`. No macOS ou Ubuntu, começa com `/dev/`.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
### <a name="install-the-required-tools-for-your-arduino-board"></a>Instalar as ferramentas necessárias para sua placa Arduino

Instale o SDK do Hub IoT do Azure na placa Arduino executando o seguinte comando:

```bash
gulp install-tools
```

Essa tarefa pode levar muito tempo para ser concluída, dependendo da sua conexão de rede.

> [!NOTE]
> Saia da instância em execução do IDE do Arduino ao executar tarefas gulp: `install-tools`, `run`.

### <a name="deploy-and-run-the-sample-app"></a>Implantar e executar o aplicativo de exemplo
Implantar e executar o aplicativo de exemplo executando o seguinte comando:

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>Verificar se o aplicativo funciona
Se você não vir o LED piscar, consulte o [guia de solução de problemas][troubleshooting-page] para ver soluções de problemas comuns.

![LED piscando][led-blinking]

## <a name="summary"></a>Resumo
Você instalou as ferramentas necessárias para trabalhar com a placa Arduino e implantou um aplicativo de exemplo para a placa Arduino para piscar o LED. Agora, você pode criar, implantar e executar outro aplicativo de exemplo que conecta sua placa Arduino ao Hub IoT do Azure para enviar e receber mensagens.

## <a name="next-steps"></a>Próximas etapas
[Obter as ferramentas do Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md


<!--HONumber=Dec16_HO2-->


