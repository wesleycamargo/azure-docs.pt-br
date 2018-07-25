---
title: Usar um Hub IoT existente com a solução de simulação de dispositivo - Azure | Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução de Simulação de Dispositivo para usar um Hub IoT existente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967466"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Usar um Hub IoT existente com o acelerador de solução de simulação de dispositivo

Ao provisionar o Acelerador de solução de simulação de dispositivo, você pode optar por implantar um hub IoT no grupo de recursos do acelerador de solução para usar na simulação.

Se não optar por implantar o IoT Hub opcional, você deverá usar seu próprio hub para qualquer simulação que executar. Se optar por implantar o Hub IoT opcional, você poderá optar por usar esse hub opcional ou seu próprio hub.

Se você não tiver um Hub IoT, será possível criar um novo por meio do [portal do Azure](https://portal.azure.com).

Para usar um Hub IoT já existente, você precisa de uma cadeia de conexão para a política de acesso compartilhado **iothubowner**. Você pode obter essa cadeia de conexão no [portal do Azure](https://portal.azure.com):

1. Na página de configuração do hub no portal, clique em **Políticas de acesso compartilhado**.
1. Clique em **iothubowner**.
1. Copie a cadeia de conexão primária ou secundária.

[![Obter a cadeia de conexão](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

USe a cadeia de conexão copiada quando você configurar a simulação:

[![Configurar a simulação](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu a usar um hub IoT existente em uma simulação. Em seguida, convém aprender a [Configurar um modelo de dispositivo personalizado](iot-accelerators-device-simulation-custom-model.md) para uma simulação.
