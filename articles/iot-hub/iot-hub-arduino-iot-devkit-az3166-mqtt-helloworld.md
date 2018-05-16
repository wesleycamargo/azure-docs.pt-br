---
title: Enviar mensagens para um servidor MQTT usando a biblioteca de clientes MQTT | Microsoft Docs.
description: Use o DevKit como um cliente para enviar mensagens para um servidor MQTT.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 073ac7cf2d8d8d5dac6a5a4f9b9d4e3d1af6f095
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Os sistemas de Internet das Coisas (IoT) normalmente lidam com conexões de Internet de qualidade ruim, intermitentes ou lentas. MQTT é um protocolo de conectividade entre máquinas (M2M), que foi desenvolvido com esses desafios em mente. 

A biblioteca de clientes MQTT usada aqui é parte do projeto [PEclipse Paho](http://www.eclipse.org/paho/), que fornece APIs para usar MQTT em vários meios de transporte.

## <a name="what-you-learn"></a>O que você aprenderá

Neste projeto, você aprenderá a:
- Como usar a biblioteca de clientes MQTT para enviar mensagens para um agente MQTT.
- Como configurar seu MXChip IoT DevKit como um cliente MQTT.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

## <a name="open-the-project-folder"></a>Abrir a pasta do projeto

1. Desconecte o DevKit de seu computador, se ele já estiver conectado.

2. Iniciar o VS Code.

3. Conecte o DevKit ao computador.

## <a name="open-the-mqttclient-sample"></a>Abra o exemplo MQTTClient

Expanda a seção **EXEMPLOS DO ARDUINO** no lado esquerdo, vá até **Exemplos para MXCHIP AZ3166 > MQTT** e selecione **MQTTClient**. É aberta uma nova janela do VS Code com a pasta de projeto nela.

> [!NOTE]
> Você também pode abrir o exemplo da paleta de comandos. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Compilar e carregar o esboço do Arduino no DevKit

Digite `Ctrl+P` (macOS: `Cmd+P`) para executar `task device-upload`. Quando o upload for concluído, o DevKit será reiniciado e executará o esboço.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Você pode receber uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse erro, consulte as [Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Teste do projeto

No VS Code, siga este procedimento para abrir e configurar o Serial Monitor:

1. Clique na palavra `COM[X]` na barra de status para definir a porta COM certa com `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone de tomada na barra de status para abrir o Serial Monitor: ![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na barra de status, clique no número que representa a Taxa de Baud e defina-a como `115200`: ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O Serial Monitor exibe todas as mensagens enviadas pelo esboço de exemplo. O esboço conecta o DevKit ao Wi-Fi. Depois que a conexão Wi-Fi for bem-sucedida, o esboço enviará uma mensagem para o agente MQTT. Depois disso, o exemplo repetidamente envia duas mensagens "iot.eclipse.org" usando QoS 0 e QoS 1, respectivamente.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou conecte-se usando canais abaixo:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Consulte também

* [Como conectar o IoT DevKit AZ3166 ao Hub IoT na nuvem]({{"/docs/getting-started/" | absolute_url }})
* [Shake, Shake para um Tweet]({{"/docs/projects/shake-shake/" | absolute_url }})

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar seu MXChip Iot DevKit como um cliente MQTT e usar a biblioteca de clientes MQTT para enviar mensagens a um agente MQTT, aqui estão as próximas etapas sugeridas:

* [Visão geral do acelerador de solução de Monitoramento Remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
