---
title: "Conectar o Arduino (C) ao IoT do Azure - Lição 1: configurar dispositivo | Microsoft Docs"
description: Configure o Adafruit Feather M0 WiFi para o primeiro uso.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "configurar arduino, conectar arduino ao pc, configuração arduino, placa arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76
ms.lasthandoff: 01/24/2017


---
# <a name="configure-your-device"></a>Configurar seu dispositivo
## <a name="what-you-will-do"></a>O que você fará
Configure sua placa Adafruit Feather M0 WiFi Arduino para o primeiro uso montando a placa e ligando-a. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-need"></a>O que você precisa
Para concluir esta operação, você precisará das seguintes partes do seu Kit de início do Adafruit Feather M0 WiFi:

* A placa Adafruit Feather M0 Wi-Fi
* Um cabo USB do tipo Micro B para Tipo A

![kit][kit]

Você também precisará de:

* Um computador executando o Windows, Mac ou Linux.
* Uma conexão sem fio com a qual a placa Arduino se conectará.
* Uma conexão com a Internet para baixar a ferramenta de configuração.

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:

* Como montar sua placa Arduino e ligá-la para as lições seguintes.
* Como adicionar permissões de porta serial no Ubuntu.

## <a name="connect-your-arduino-board-to-your-computer"></a>Conectar sua placa Arduino ao computador

1. Conecte o cabo micro USB à porta micro USB superior.

   ![Porta micro USB superior][top-micro-usb-port]

2. Conecte a outra extremidade do cabo USB ao computador.

   ![USB do computador][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Adicionar permissões de porta serial no Ubuntu

Você pode ignorar esta seção se usar Windows ou macOS. Para o Ubuntu, você precisa das seguintes etapas para certificar-se de que o usuário normal do Linux tenha as permissões para operar na porta USB da placa Arduino.

1. Agora, como usuário normal do terminal:

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   Você verá algo semelhante a:

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   O "0" pode ser um número diferente ou várias entradas podem ser retornadas. No primeiro caso, os dados de que precisamos são `uucp`, no segundo são `dialout`, que é o proprietário do grupo do arquivo.

2. Adicionar usuário ao grupo:

   ```bash
   sudo usermod -a -G group-name username
   ```

   Em que `group-name` são os dados encontrados na primeira etapa e `username` é o nome de usuário do Linux.

3. Você precisará fazer logoff e fazer logon novamente para que esta alteração entre em vigor e a instalação seja concluída.

## <a name="summary"></a>Resumo
Neste artigo, você aprendeu como configurar sua placa Arduino. A próxima tarefa é instalar as ferramentas e o software necessários para preparar a execução de um aplicativo de exemplo na placa Arduino.

![O hardware está pronto][hardware-is-ready]

## <a name="next-steps"></a>Próximas etapas
[Obter as ferramentas][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
