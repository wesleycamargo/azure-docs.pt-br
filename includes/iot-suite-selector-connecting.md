---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28290718d15a893c5d676c887b9f810449075746
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665983"
---
> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C em Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js (genérico)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [C no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

Neste tutorial, você implementará um dispositivo **Resfriador** que envia a seguinte telemetria para o [acelerador de solução](../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) de Monitoramento Remoto:

* Temperatura
* Pressão
* Umidade

Para simplificar, o código gera valores de telemetria de exemplo para o **Resfriador**. Você pode estender o exemplo conectando sensores reais ao dispositivo e enviando telemetria real.

O dispositivo de exemplo também:

* Envia os metadados à solução para descrever seus recursos.
* Responde às ações disparadas da página **Dispositivos** na solução.
* Responde às alterações de configuração da página **Dispositivos** na solução.

Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para o dispositivo, implante o acelerador de solução de Monitoramento Remoto e adicione um novo dispositivo físico à solução.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Implantar o acelerador de solução de Monitoramento Remoto

O dispositivo **Resfriador** criado neste tutorial envia os dados para uma instância do acelerador de solução de [Monitoramento Remoto](../articles/iot-accelerators/iot-accelerators-remote-monitoring-explore.md). Caso ainda não tenha provisionado o acelerador de solução de monitoramento remoto em sua conta do Azure, confira [Implantar o acelerador de solução de monitoramento remoto](../articles/iot-accelerators/iot-accelerators-remote-monitoring-deploy.md)

Quando o processo de implantação para a solução de Monitoramento Remoto for concluído, clique em **Inicialização** para abrir o painel da solução em seu navegador.

![O painel da solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Adicionar o dispositivo à solução de Monitoramento Remoto

> [!NOTE]
> Se você já adicionou um dispositivo em sua solução, poderá ignorar esta etapa. No entanto, a próxima etapa exige a cadeia de conexão do dispositivo. É possível recuperar a cadeia de conexão de um dispositivo a partir do [Portal do Azure](https://portal.azure.com) ou usando uma ferramente da CLI [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest).

Para que um dispositivo se conecte ao acelerador de solução, ele precisa se identificar para o Hub IoT usando credenciais válidas. É possível salvar a cadeia de conexão do dispositivo contendo essas credenciais, quando você adiciona o dispositivo à solução. Você incluirá a cadeia de conexão de dispositivo no seu aplicativo cliente, mais adiante neste tutorial.

Para adicionar um dispositivo à sua solução de Monitoramento Remoto, conclua as seguintes etapas na página **Dispositivos** na solução:

1. Escolha **+ Novo dispositivo** e, em seguida, escolha **Físico** como o **Tipo de dispositivo**:

    ![Adicionar um dispositivo físico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Insira **Physical-chiller** como a ID do dispositivo. Escolha as opções **Chave Simétrica** e **Gerar chaves automaticamente**:

    ![Escolher as opções do dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

1. Escolha **Aplicar**. Em seguida, anote os valores **ID do dispositivo**, **Chave Primária** e **Cadeia de conexão – Chave primária**:

    ![Recuperar credenciais](media/iot-suite-selector-connecting/credentials.png)

Agora, você adicionou um dispositivo físico ao acelerador de solução de Monitoramento Remoto e anotou a cadeia de conexão do dispositivo. Nas seções a seguir, você implementará o aplicativo cliente que usa a cadeia de conexão do dispositivo para conectar-se à solução.

O aplicativo cliente implementa o modelo de dispositivo **Resfriador** interno. Um modelo de dispositivo de acelerador de solução especifica o seguinte sobre um dispositivo:

* As propriedades que o dispositivo relata à solução. Por exemplo, um dispositivo **Resfriador** relata informações sobre seu firmware e sua localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um dispositivo **Resfriador** envia valores de temperatura, umidade e pressão.
* Os métodos que você pode agendar na solução para serem executados no dispositivo. Por exemplo, um dispositivo **Resfriador** deve implementar os métodos **Reiniciar**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressure**.