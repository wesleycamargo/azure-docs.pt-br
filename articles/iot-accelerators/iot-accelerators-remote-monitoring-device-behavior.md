---
title: Comportamento de dispositivo simulado em solução de monitoramento remoto - Azure | Microsoft Docs
description: Este artigo descreve como usar JavaScript para definir o comportamento de um dispositivo simulado na solução de monitoramento remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 5c05f2617025d5cb4f1328f04c8d71049e1efcc7
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284768"
---
# <a name="implement-the-device-model-behavior"></a>Implementar o comportamento do modelo do dispositivo

O artigo [Entenda o esquema de modelo do dispositivo](iot-accelerators-remote-monitoring-device-schema.md) descreve o esquema que define um modelo de dispositivo simulado. Esse artigo referenciou dois tipos de arquivo JavaScript que implementam o comportamento de um dispositivo simulado:

- **Estado** arquivos JavaScript que executam a intervalos fixos para atualizar o estado interno do dispositivo.
- **Método** arquivos JavaScript que são executados quando a solução invoca um método no dispositivo.

Neste artigo, você aprenderá a:

>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de Monitoramento Remoto
> * Depurar seus scripts

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como definir o comportamento do seu próprio modelo de dispositivo simulado personalizado. Este artigo mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de Monitoramento Remoto
> * Depurar seus scripts

Agora que você aprendeu como especificar o comportamento de um dispositivo simulado, a próxima etapa sugerida é saber como [Criar um dispositivo simulado](iot-accelerators-remote-monitoring-test.md).

Para obter informações do desenvolvedor sobre a solução de Monitoramento Remoto, confira:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Solução de Problemas do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
