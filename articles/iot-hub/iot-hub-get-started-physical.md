---
title: "Introdução à conexão de dispositivos físicos ao Hub IoT do Azure | Microsoft Docs"
description: "Saiba como criar dispositivos IoT físicos e conectá-los ao Hub IoT do Azure. Os dispositivos podem enviar telemetria ao Hub IoT e o Hub IoT pode monitorar e gerenciar seus dispositivos."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: tutorial do hub iot do azure
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 2e00affad9302145ac0c62e5994bd636cb2b72a9
ms.contentlocale: pt-br
ms.lasthandoff: 06/03/2017


---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Tutoriais de introdução aos dispositivos físicos no Hub IoT do Azure

Estes tutoriais apresentam a você o Hub IoT do Azure e os SDKs do dispositivo. Os tutoriais abordam os cenários comuns de IoT para demonstrar os recursos do Hub IoT. Os tutoriais também ilustram como combinar o Hub IoT com outros serviços e ferramentas do Azure para criar soluções de IoT mais eficazes. Os tutoriais listados na tabela a seguir mostram como criar dispositivos IoT físicos.

| Dispositivo IoT                       | Linguagem de programação |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

Além disso, você pode usar um gateway IoT Edge para habilitar os dispositivos a conectarem-se ao seu Hub IoT.

| Dispositivos de gateway               | Linguagem de programação | Plataforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modelo DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

