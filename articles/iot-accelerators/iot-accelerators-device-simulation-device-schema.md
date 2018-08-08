---
title: Esquema do dispositivo na solução de simulação de dispositivo - Azure | Microsoft Docs
description: Este artigo descreve o esquema JSON que define um dispositivo simulado na solução de simulação de dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: a27624d4c7a0bde4b33aefe8d05881b743ce397d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295141"
---
# <a name="understand-the-device-model-schema"></a>Compreender o esquema de modelo do dispositivo

Você pode usar o Acelerador de solução de simulação de dispositivo para gerar cargas de teste para soluções que usam o IoT Hub. Quando você implanta a solução Device Simulation, uma coleção de dispositivos simulados é provisionada automaticamente. Você pode personalizar os dispositivos simulados existentes ou criar o seu próprio.

Este artigo descreve o esquema de modelo do dispositivo que especifica os recursos e o comportamento de um dispositivo simulado. O modelo de dispositivo é armazenado em um arquivo JSON.

Os artigos a seguir estão relacionados ao artigo atual:

* [Implementar o comportamento de modelo do dispositivo](iot-accelerators-device-simulation-device-behavior.md) descreve os arquivos JavaScript que são utilizados para implementar o comportamento de um dispositivo simulado.
* [Criar um novo dispositivo simulado](iot-accelerators-remote-monitoring-test.md) reúne tudo e mostra como implementar um novo tipo de dispositivo simulado para sua solução.

Neste artigo, você aprenderá a:

>[!div class="checklist"]
> * Usar um arquivo JSON para definir um modelo de dispositivo simulado
> * Especificar as propriedades do dispositivo simulado
> * Especificar a telemetria que o dispositivo simulado envia
> * Especificar os métodos de nuvem para dispositivo em que o dispositivo responde

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como criar seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar um arquivo JSON para definir um modelo de dispositivo simulado
> * Especificar as propriedades do dispositivo simulado
> * Especificar a telemetria que o dispositivo simulado envia
> * Especificar os métodos de nuvem para dispositivo em que o dispositivo responde

Agora que você aprendeu sobre o esquema JSON, a próxima etapa sugerida é saber como [implementar o comportamento do seu dispositivo simulado](iot-accelerators-device-simulation-device-behavior.md).

Para obter mais informações para desenvolvedores sobre a solução de simulação de dispositivo, consulte a [guia de referência do desenvolvedor](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
