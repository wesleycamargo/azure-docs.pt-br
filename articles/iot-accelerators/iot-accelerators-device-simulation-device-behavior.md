---
title: Comportamento do dispositivo simulado na solução de simulação de dispositivo - Azure | Microsoft Docs
description: Este artigo descreve como usar o JavaScript para definir o comportamento de um dispositivo simulado na solução de simulação de dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b68550bce1f4e3fbe3845c21598720083c8e384c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295139"
---
# <a name="implement-the-device-model-behavior"></a>Implementar o comportamento do modelo do dispositivo

O artigo [Entenda o esquema de modelo do dispositivo](iot-accelerators-device-simulation-device-schema.md) descreve o esquema que define um modelo de dispositivo simulado. Esse artigo referenciou dois tipos de arquivo JavaScript que implementam o comportamento de um dispositivo simulado:

- **Estado**: arquivos JavaScript que são executados em intervalos fixos para atualizar o estado interno do dispositivo.
- **Método**: arquivos JavaScript que são executados quando a solução invoca um método no dispositivo.

Neste artigo, você aprenderá a:

>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método do hub da IoT ao qual está conectado
> * Depurar seus scripts

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como definir o comportamento do seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método do hub da IoT ao qual está conectado
> * Depurar seus scripts

Agora que você aprendeu como especificar o comportamento de um dispositivo simulado, a próxima etapa sugerida é saber como [criar um dispositivo simulado](iot-accelerators-remote-monitoring-test.md).

Para obter mais informações para desenvolvedores sobre a solução de simulação de dispositivo, consulte a [guia de referência do desenvolvedor](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
