---
title: "Conecte o Arduino (C) ao IoT do Azure - Introdução | Microsoft Docs"
description: "Veja uma introdução ao Adafruit Feather M0 WiFi, crie seu Hub IoT do Azure e conecte o Adafruit Feather M0 WiFi ao Hub IoT"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "hub iot do azure, introdução à Internet das coisas, tutorial de Internet das coisas, Internet das coisas adafruit, introdução ao arduino"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 6789e6f1c094f8809163e29349c8ea54e1e97683


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Introdução à placa Arduino: Adafruit Feather M0 WiFi

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com sua placa Arduino. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md).

## <a name="lesson-1-configure-your-device"></a>Lição 1: Configurar seu dispositivo
![Diagrama de ponta a ponta da Lição 1][Lesson-1-end-to-end-diagram]

Nesta lição, você configura sua placa Arduino com um sistema operacional, configura o ambiente de desenvolvimento e implanta um aplicativo para sua placa Arduino.

### <a name="configure-your-device"></a>Configurar seu dispositivo
Configure sua placa Arduino para o primeiro uso montando a placa e ligando-a.

*Tempo estimado para conclusão: 5 minutos*

Vá até [Configurar seu dispositivo][configure-your-device].

### <a name="get-the-tools"></a>Obter as ferramentas
Baixe as ferramentas e o software para compilar e implantar seu primeiro aplicativo para a placa Arduino.

*Tempo estimado para conclusão: 20 minutos*

Acesse [Obter as ferramentas][get-the-tools]

### <a name="create-and-deploy-the-blink-application"></a>Criar e implantar o aplicativo de piscar
Clone o aplicativo de piscar de exemplo do Arduino do GitHub e use o gulp para implantar o aplicativo na placa Arduino. Esse aplicativo de exemplo pisca o LED conectado à placa no GPIO núm. 13 a cada dois segundos.

*Tempo estimado para conclusão: 5 minutos*

Vá até [Criar e implantar o aplicativo de piscar][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Lição 2: Criar seu hub IoT
![Diagrama de ponta a ponta da Lição 2][lesson-2-end-to-end-diagram]

Nesta lição, você cria sua conta gratuita do Azure, provisiona o Hub IoT do Azure e cria seu primeiro dispositivo no Hub IoT.

Conclua a Lição 1 antes de iniciar esta lição.

### <a name="get-the-azure-tools"></a>Obter as ferramentas do Azure
Instalar a interface de linha de comando do Azure (CLI do Azure).

*Tempo estimado para conclusão: 10 minutos*

Vá até [Obter ferramentas do Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>Criar seu Hub IoT e registrar sua placa Arduino
Crie seu grupo de recursos, provisione seu primeiro Hub IoT do Azure e adicione seu primeiro dispositivo ao Hub IoT usando a CLI do Azure.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Criar seu Hub IoT e registrar sua placa Arduino][create-your-iot-hub-and-register-your-arduino-board].

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lição 3: Enviar mensagens do dispositivo para a nuvem
![Diagrama de ponta a ponta da Lição 3][lesson-3-end-to-end-diagram]

Nesta lição, você envia mensagens da placa Arduino para o Hub IoT. Você também cria um aplicativo de funções do Azure que obtém as mensagens recebidas de seu Hub IoT e as grava no Armazenamento de Tabelas do Azure.

Conclua a Lição 1 e a Lição 2 antes de iniciar esta lição.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure
Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Executar um aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem
Implante e execute um aplicativo de exemplo em sua placa Arduino que envia mensagens ao Hub IoT.

*Tempo estimado para conclusão: 10 minutos*

Acesse [Executar o aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens mantidas no Armazenamento do Azure
Monitore as mensagens do dispositivo para a nuvem conforme são gravadas no Armazenamento do Azure.

*Tempo estimado para conclusão: 5 minutos*

Vé até [Ler mensagens persistentes no Armazenamento do Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lição 4: Enviar mensagens da nuvem para o dispositivo
![Diagrama de ponta a ponta da Lição 4][lesson-4-end-to-end-diagram]

Essa lição mostra como enviar mensagens do Hub IoT do Azure para a placa Arduino. As mensagens de controlam o comportamento de ligar e desligar do LED no GPIO núm. 13 da placa. Um aplicativo de exemplo está preparado para você realizar essa tarefa.

Conclua a Lição 1, a Lição 2 e a Lição 3 antes de iniciar esta lição.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Executar o aplicativo de exemplo para receber mensagens da nuvem para o dispositivo
O aplicativo de exemplo na Lição 4 é executado em sua placa Arduino e monitora mensagens de entrada de seu Hub IoT. Uma nova tarefa gulp envia mensagens para dua placa Arduino seu Hub IoT para piscar o LED.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Executar o aplicativo de exemplo para receber mensagens da nuvem para o dispositivo][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Seção opcional: alterar o comportamento de ativar e desativar do LED
Personalize as mensagens para alterar o comportamento liga e desliga do LED.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Seção opcional: alterar o comportamento de ativar e desativar do LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Solucionar problemas
Se tiver problemas durante as lições, procure por soluções no artigo [Solução de problemas][troubleshooting].

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md


<!--HONumber=Jan17_HO4-->


