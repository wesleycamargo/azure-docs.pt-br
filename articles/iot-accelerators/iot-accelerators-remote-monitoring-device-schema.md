---
title: Esquema de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo descreve o esquema JSON que define um dispositivo simulado na solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: f312f29e14c371e7b500f3eee6471151e3544513
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338848"
---
# <a name="understand-the-device-model-schema"></a>Compreender o esquema de modelo do dispositivo

É possível usar dispositivos simulados na solução de monitoramento remoto para testar o comportamento. Quando você implanta a solução de monitoramento remoto, uma coleção de dispositivos simulados é provisionada automaticamente. Você pode personalizar os dispositivos simulados existentes ou criar o seu próprio.

Este artigo descreve o esquema de modelo do dispositivo que especifica os recursos e o comportamento de um dispositivo simulado. O modelo de dispositivo é armazenado em um arquivo JSON.

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
