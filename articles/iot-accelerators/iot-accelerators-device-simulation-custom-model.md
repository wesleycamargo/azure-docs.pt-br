---
title: Configurar um modelo de dispositivo personalizado – Azure | Microsoft Docs
description: Este artigo descreve como configurar um modelo de dispositivo personalizado no Acelerador de solução de Simulação de Dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967462"
---
# <a name="device-models"></a>Modelos de dispositivo

Ao configurar uma simulação, você pode optar por usar um modelo de dispositivo existente com um conjunto predefinido de sensores ou criar um modelo de dispositivo personalizado com sua opção de sensores simulados. Sensores personalizados permitem que você modele de forma mais aproximada seus dispositivos reais.

## <a name="pre-configured-device-models"></a>Modelos de dispositivos pré-configurados

O acelerador de solução de Simulação de Dispositivo fornece três modelos de dispositivo pré-configurados: resfriadores, elevadores e caminhões.

Modelos de dispositivos pré-configurados têm vários sensores com comportamentos avançados definidos em um arquivo JavaScript. Você não pode configurar esses comportamentos na IU da Web.

A tabela a seguir mostra as configurações para cada modelo de dispositivo pré-configurado:

| Modelo do dispositivo  | Sensor           | Unidade  |
| ------------- | ---------------- | ----- |
| Resfriador       | umidade         | %     |
|               | pressão         | psig  |
|               | temperatura      | F     |
| Elevador      | Piso            |       |
|               | Vibração        | MM    |
|               | Temperatura      | F     |
| Caminhão         | Latitude         |       |
|               | Longitude        |       |
|               | velocidade            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>Modelos de dispositivos personalizados

Modelos de dispositivos personalizados permitem modelar sensores que modelam de forma mais aproximada seus próprios dispositivos. Um dispositivo personalizado pode ter até 10 sensores personalizados.

Ao selecionar o tipo de modelo de dispositivo personalizado, você adiciona novos sensores clicando em **+Adicionar sensor**.

[![Configurar os sensores](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Os sensores personalizados têm as seguintes propriedades:

| Campo     | DESCRIÇÃO |
| --------- | ----------- |
| Nome do sensor | Um nome amigável para o sensor, como **temperatura** ou **velocidade**.  |
| Comportamento  | Os comportamentos permitem que os dados de telemetria variem de uma mensagem para a próxima para simular dados reais. O **Incremento** aumenta o valor em um para cada mensagem enviada começando no valor mínimo. Depois que o valor máximo é atendido, ele começa novamente no valor mínimo. O **Decremento** comporta-se da mesma maneira que o **Incremento**, mas com contagem regressiva. O comportamento **Aleatório** gera um valor aleatório entre o valor máximo e o valor mínimo. |
| Valor mínimo | O número mais baixo no intervalo aceitável. |
| Valor máximo | O maior número no intervalo aceitável. |
| Unidade      | A unidade de medida do sensor como °F ou MPH. |

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu a configurar um modelo de dispositivo personalizado para uma simulação. Em seguida, convém explorar alguns dos outros [aceleradores de solução IoT](about-iot-accelerators.md).