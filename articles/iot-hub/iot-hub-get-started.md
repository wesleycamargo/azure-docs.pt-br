---
title: Hub IoT do Azure – introdução à conexão de dispositivos IoT à nuvem | Microsoft Docs
description: Saiba como conectar seus quadros IoT e kits de início ao Hub IoT do Azure. Seus dispositivos podem enviar telemetria ao Hub IoT e o Hub IoT pode monitorar e gerenciar seus dispositivos.
author: dominicbetts
manager: timlt
keywords: tutorial do hub iot do azure
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634695"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Introdução ao Hub IoT do Azure com dispositivos reais

Você pode usar o Hub IoT do Azure e os SDKs do dispositivo IoT do Azure para criar soluções de IoT (Internet das Coisas):

* O Hub IoT do Azure é um serviço completamente gerenciado na nuvem que se conecta com segurança aos seus dispositivos IoT, monitorando-os e gerenciando-os. Use os SDKs do dispositivo IoT do Azure para implementar os dispositivos IoT.
* Use um gateway IoT em cenários de IoT mais complexos. Por exemplo, quando você precisa considerar fatores como dispositivos herdados, custos de largura de banda, políticas de segurança e privacidade ou processamento de dados de borda. Nesses cenários, use o [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) para implementar um gateway que conecte dispositivos ao seu hub IoT.

## <a name="what-the-how-to-articles-cover"></a>O que aborda os artigos de instruções

Estes artigos apresentam a você o Hub IoT do Azure e os SDKs do dispositivo. Os artigos abordam os cenários comuns de IoT para demonstrar os recursos do Hub IoT. Os artigos também ilustram como combinar o Hub IoT com outros serviços e ferramentas do Azure para criar soluções de IoT mais eficazes. Nos artigos, você usa dispositivos IoT reais.

## <a name="set-up-your-device"></a>Configurar seu dispositivo

Conectar um dispositivo IoT ou gateway ao Hub IoT do Azure:

| Dispositivo IoT                       | Linguagem de programação |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Kit de Desenvolvimento da IoT                       | [Arduino no VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Simulador de dispositivo online         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
