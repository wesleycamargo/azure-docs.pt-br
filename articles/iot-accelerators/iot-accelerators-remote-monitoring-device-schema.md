---
title: Esquema de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo descreve o esquema JSON que define um dispositivo simulado na solução de monitoramento remoto.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 3c2bca45a7927689992cf44fb70199e246641760
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445415"
---
# <a name="understand-the-device-model-schema"></a>Compreender o esquema de modelo do dispositivo

É possível usar dispositivos simulados na solução de monitoramento remoto para testar o comportamento. A solução de Monitoramento Remoto inclui um serviço de simulação de dispositivo para executar dispositivos simulados. Quando você implanta a solução de monitoramento remoto, uma coleção de dispositivos simulados é provisionada automaticamente. Você pode personalizar os dispositivos simulados existentes ou criar o seu próprio.

Este artigo descreve o esquema de modelo do dispositivo que especifica os recursos e o comportamento de um dispositivo simulado. O modelo de dispositivo é armazenado em um arquivo JSON.

> [!NOTE]
> Esse esquema de modelo de dispositivo é apenas para dispositivos simulados hospedados no serviço de simulação de dispositivo. Se você quiser criar um dispositivo real, veja [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).

Os artigos a seguir estão relacionados ao artigo atual:

* [Implementar o comportamento de modelo do dispositivo](iot-accelerators-remote-monitoring-device-behavior.md) descreve os arquivos JavaScript que são utilizados para implementar o comportamento de um dispositivo simulado.
* [Criar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) reúne tudo e mostra como implementar um novo tipo de dispositivo simulado para sua solução.

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

Agora que você aprendeu sobre o esquema JSON, a próxima etapa sugerida é saber como [implementar o comportamento do dispositivo simulado](iot-accelerators-remote-monitoring-device-behavior.md).

Para obter informações do desenvolvedor sobre a solução de Monitoramento Remoto, confira:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Solução de Problemas do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
