---
title: Simulação de dispositivo com monitoramento remoto do IoT - Azure | Microsoft Docs
description: Este guia de instruções mostra como usar o simulador de dispositivo com o acelerador de solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/28/2018
ms.topic: conceptual
ms.openlocfilehash: 06909c06087219cdfa6edccbfed70ea1694e5a03
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633718"
---
# <a name="create-and-test-a-new-simulated-device"></a>Criar e testar um novo dispositivo simulado

O acelerador de solução de Monitoramento Remoto permite que você defina seus próprios dispositivos simulados. Este artigo mostra como definir um novo dispositivo de lâmpada simulado e, em seguida, testá-lo localmente. O acelerador de solução inclui dispositivos simulados, como resfriadores e caminhões. No entanto, você pode definir seus próprios dispositivos simulados para testar as soluções de IoT antes de implantar dispositivos reais.

> [!NOTE]
> Este artigo descreve como usar dispositivos simulados hospedados no serviço de simulação do dispositivo. Se você quiser criar um dispositivo físico, confira [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).

Este guia de instruções mostra como personalizar o microsserviço de simulação do dispositivo. Esse microsserviço faz parte do acelerador de solução de Monitoramento Remoto. Para mostrar os recursos de simulação do dispositivo, este guia de instruções usa dois cenários no aplicativo de IoT da Contoso:

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>Próximas etapas

Este guia mostrou como criar um tipo de dispositivo simulado personalizado e testá-lo executando o microsserviço de simulação do dispositivo localmente.

A próxima etapa recomendada é saber como implantar os tipos de dispositivos simulados personalizados no [Acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
