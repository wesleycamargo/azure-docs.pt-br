---
title: Gerenciamento de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como gerenciar dispositivos conectados à solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 0f177c3a8746f801e52cdac6cb2189e9cc28e1e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627272"
---
# <a name="manage-and-configure-your-devices"></a>Gerenciar e configurar dispositivos

Este tutorial mostra os recursos de gerenciamento de dispositivo da solução de Monitoramento Remoto. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso solicitou novas máquinas para expandir uma de suas instalações a fim de aumentar os resultados. Enquanto espera a entrega das novas máquinas, você pode executar uma simulação para verificar o comportamento da solução. Como operador, você quer gerenciar e configurar os dispositivos na solução de Monitoramento Remoto.

Para fornecer uma maneira extensível de gerenciar e configurar dispositivos, a solução de Monitoramento Remoto usa recursos do Hub IoT como [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) e [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md). Para saber como um desenvolvedor de dispositivos implementa métodos em um dispositivo físico, confira [Personalizar o acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize.md).

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Provisionar um dispositivo simulado.
> * Testar o dispositivo simulado.
> * Chamar os métodos de dispositivo por meio da solução.
> * Reconfigurar um dispositivo.

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada da solução de Monitoramento Remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Implantar o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Navegue até a página **Dispositivos** na solução e, em seguida, selecione **+ Novo dispositivo**. No painel **Novo dispositivo**, escolha **Simulado**:

![Provisionar um dispositivo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovision.png)

Deixe o número de dispositivos a serem provisionados definido como **1**. Escolha o modelo de dispositivo **Mecanismo Defeituoso** e **Aplicar** para criar o dispositivo simulado:

![Provisionar um dispositivo de mecanismo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine.png)

Para saber como provisionar um dispositivo *físico*, confira [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para exibir detalhes do novo dispositivo simulado, selecione-o na lista de dispositivos na página **Dispositivos**. Informações sobre o dispositivo são exibidas no painel **Detalhes do dispositivo**:

![Exibir o novo dispositivo de mecanismo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew.png)

Em **detalhes do dispositivo**, verifique se o novo dispositivo está enviando telemetria. Para exibir os diferentes fluxos de telemetria do seu dispositivo, escolha um nome de telemetria como **vibração**:

![Selecionar um fluxo de telemetria a ser exibido](./media/iot-accelerators-remote-monitoring-manage/devicesvibration.png)

O painel **Detalhes do dispositivo** exibe outras informações sobre o dispositivo como valores de marca, os métodos com suporte e as propriedades relatadas pelo dispositivo.

Para exibir um diagnóstico detalhado, role para baixo até a exibição **Diagnóstico**.

## <a name="act-on-a-device"></a>Agir em um dispositivo

Para agir em um ou mais de um dispositivo, selecione-o(s) na lista de dispositivos e, em seguida, escolha **Trabalhos**. O modelo do dispositivo do **Mecanismo** especifica três métodos que precisam ter suporte em um dispositivo:

![Métodos de mecanismo](./media/iot-accelerators-remote-monitoring-manage/devicesmethods.png)

Escolha **FillTank**, defina o nome do trabalho como **FillEngineTank** e escolha **Aplicar**:

![Agendar o método reiniciar](./media/iot-accelerators-remote-monitoring-manage/devicesrestartengine.png)

Para acompanhar o status do trabalho na página **Manutenção**, escolha **Trabalhos**:

![Monitorar o trabalho de agendas](./media/iot-accelerators-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Métodos em outros dispositivos

Ao explorar os tipos de dispositivo simulado diferentes, você verá que outros tipos de dispositivo dão suporte a métodos diferentes. Em uma implantação com dispositivos físicos, o modelo de dispositivo especifica os métodos que precisam ter suporte no dispositivo. Normalmente, o desenvolvedor do dispositivo é responsável por desenvolver o código que faz com que o dispositivo aja em resposta a uma chamada de método.

Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista da página **Dispositivos**. O painel **Trabalhos** mostra os tipos de método comuns a todos os dispositivos selecionados.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para alterar a configuração de um dispositivo, selecione-o na lista de dispositivos da página **Dispositivos**, escolha **Trabalhos** e depois **Reconfigurar**. O painel Trabalhos mostra os valores de propriedade do dispositivo selecionado que podem ser alterados:

![Reconfigurar um dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure.png)

Para fazer uma alteração, adicione um nome para o trabalho, atualize os valores de propriedade e escolha **Aplicar**:

![Atualizar um valor da propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical.png)

Para acompanhar o status do trabalho na página **Manutenção**, escolha **Trabalhos**.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Provisionar um dispositivo simulado.
> * Testar o dispositivo simulado.
> * Chamar os métodos de dispositivo por meio da solução.
> * Reconfigurar um dispositivo.

Agora que você aprendeu a gerenciar seus dispositivos, as próximas etapas sugeridas são aprender como:

* [Solucionar e corrigir problemas de dispositivo](iot-accelerators-remote-monitoring-maintain.md).
* [Testar sua solução com dispositivos simulados](iot-accelerators-remote-monitoring-test.md).
* [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->