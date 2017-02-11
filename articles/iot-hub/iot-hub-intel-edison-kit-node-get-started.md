---
title: "Introdução ao Kit de início de IoT do Azure para Intel Edison | Microsoft Docs"
description: "Veja uma introdução ao Intel Edison, crie seu Hub IoT do Azure e conecte o Edison ao Hub IoT"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "desenvolvimento de intel edison, hub iot do azure, introdução à Internet das coisas, tutorial de Internet das coisas, Internet das coisas adafruit, intel edison arduino, introdução ao arduino"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: cb18052f74528e245264bb2f400b625fe550ed85
ms.openlocfilehash: 61f14728d5b985815b64b2c13c7f0df42e4de485


---
# <a name="get-started-with-intel-edison-nodejs"></a>Introdução ao Intel Edison (Node.js)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com o Intel Edison. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md).

Não tem um dispositivo ainda? Comece [aqui](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="lesson-1-configure-your-device"></a>Lição 1: Configurar seu dispositivo
![Diagrama de ponta a ponta da Lição 1](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

Nesta lição, você configura o Intel Edison com um sistema operacional, configura o ambiente de desenvolvimento e implanta um aplicativo no Edison.

### <a name="configure-your-device"></a>Configurar seu dispositivo
Configure o Intel Edison para o primeiro uso montando a placa, ligando-a e instalando a ferramenta de configuração no sistema operacional da área de trabalho para atualizar o firmware do Edison, definir sua senha e conectá-lo ao Wi-Fi.  

*Tempo estimado para conclusão: 30 minutos*

Vá até [Configurar seu dispositivo][configure-your-device].

### <a name="get-the-tools"></a>Obter as ferramentas
Baixe as ferramentas e o software para compilar e implantar seu primeiro aplicativo no Intel Edison.

*Tempo estimado para conclusão: 20 minutos*

Vá até [Obter as ferramentas][get-the-tools].

### <a name="create-and-deploy-the-blink-application"></a>Criar e implantar o aplicativo de piscar
Clone o aplicativo de piscar de exemplo do GitHub e use o gulp para implantar o aplicativo na placa do Intel Edison. Esse aplicativo de exemplo pisca o LED conectado à placa a cada dois segundos.

*Tempo estimado para conclusão: 5 minutos*

Vá até [Criar e implantar o aplicativo de piscar][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Lição 2: Criar seu hub IoT
![Diagrama de ponta a ponta da Lição 2](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

Nesta lição, você cria sua conta gratuita do Azure, provisiona o Hub IoT do Azure e cria seu primeiro dispositivo no Hub IoT.

Conclua a Lição 1 antes de iniciar esta lição.

### <a name="get-the-azure-tools"></a>Obter as ferramentas do Azure
Instalar a interface de linha de comando do Azure (CLI do Azure).

*Tempo estimado para conclusão: 10 minutos*

Vá até [Obter ferramentas do Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-intel-edison"></a>Criar seu Hub IoT e registrar o Intel Edison
Crie seu grupo de recursos, provisione seu primeiro Hub IoT do Azure e adicione seu primeiro dispositivo ao Hub IoT usando a CLI do Azure.

*Tempo estimado para conclusão: 10 minutos*

Acesse [Criar seu Hub IoT e registrar o Intel Edison](iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lição 3: Enviar mensagens do dispositivo para a nuvem
![Diagrama de ponta a ponta da Lição 3](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

Nesta lição, você envia mensagens do Edison para o Hub IoT. Você também cria um aplicativo de funções do Azure que obtém as mensagens recebidas de seu Hub IoT e as grava no Armazenamento de Tabelas do Azure.

Conclua a Lição 1 e a Lição 2 antes de iniciar esta lição.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure
Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Executar um aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem
Implante e execute um aplicativo de exemplo para seu dispositivo Intel Edison que envia mensagens ao Hub IoT.

*Tempo estimado para conclusão: 10 minutos*

Acesse [Executar o aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens mantidas no Armazenamento do Azure
Monitore as mensagens do dispositivo para a nuvem conforme são gravadas no Armazenamento do Azure.

*Tempo estimado para conclusão: 5 minutos*

Vé até [Ler mensagens persistentes no Armazenamento do Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lição 4: Enviar mensagens da nuvem para o dispositivo
![Diagrama de ponta a ponta da Lição 4](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

Esta lição mostra como enviar mensagens de seu Hub IoT do Azure para o Intel Edison. As mensagens controlam o comportamento de ativar e desativar do LED que está conectado ao Edison. Um aplicativo de exemplo está preparado para você realizar essa tarefa.

Conclua a Lição 1, a Lição 2 e a Lição 3 antes de iniciar esta lição.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Executar o aplicativo de exemplo para receber mensagens da nuvem para o dispositivo
O aplicativo de exemplo na Lição 4 é executado no Edison e monitora mensagens de entrada de seu Hub IoT. Uma nova tarefa gulp envia mensagens para o Edison de seu Hub IoT para piscar o LED.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Executar o aplicativo de exemplo para receber mensagens da nuvem para o dispositivo][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Seção opcional: alterar o comportamento de ativar e desativar do LED
Personalize as mensagens para alterar o comportamento liga e desliga do LED.

*Tempo estimado para conclusão: 10 minutos*

Vá até [Seção opcional: alterar o comportamento de ativar e desativar do LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Solucionar problemas
Se tiver problemas durante as lições, procure por soluções no artigo [Solução de problemas][troubleshooting].
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md


<!--HONumber=Dec16_HO2-->


