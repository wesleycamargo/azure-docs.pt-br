---
title: "Gerenciamento de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como gerenciar dispositivos conectados à solução de monitoramento remoto."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/06/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e0b16a30bfd3b7ed711bcb1cc955d4eccf09fac2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="manage-and-configure-your-devices"></a>Gerenciar e configurar dispositivos

Este tutorial mostra os recursos de gerenciamento de dispositivo da solução de monitoramento remoto. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso solicitou novas máquinas para expandir uma de suas instalações a fim de aumentar os resultados. Enquanto espera a entrega das novas máquinas, você pode executar uma simulação para verificar o comportamento da solução. Como operador, você deseja gerenciar e configurar os dispositivos na solução de monitoramento remoto.

Para fornecer uma maneira extensível de gerenciar e configurar dispositivos, a solução de monitoramento remoto usa recursos do Hub IoT como [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) e [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md). Para saber como um desenvolvedor de dispositivo implementa métodos em um dispositivo físico, consulte [Personalizar a solução de monitoramento remoto pré-configurada](iot-suite-remote-monitoring-customize.md).

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Provisionar um dispositivo simulado.
> * Testar o dispositivo simulado.
> * Chamar os métodos de dispositivo por meio da solução.
> * Reconfigurar um dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

## <a name="provision-a-simulated-device"></a>Provisionar um dispositivo simulado

Navegue até a página **Dispositivos** na solução e, em seguida, selecione **Provisionar**. No painel **Provisionar** escolha **Simulado**:

![Provisionar um dispositivo simulado](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

Deixe o número de dispositivos a serem provisionados definido como **1**. Escolha **Mecanismo** como o **Modelo de dispositivo** e, em seguida, escolha **Aplicar** para criar o dispositivo simulado:

![Provisionar um dispositivo de mecanismo simulado](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

Para saber como provisionar um dispositivo *físico*, consulte [Conectar seu dispositivo à solução pré-configurada de monitoramento remoto](iot-suite-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para exibir detalhes do novo dispositivo simulado, selecione-o na lista de dispositivos na página **Dispositivos**. Informações sobre o dispositivo são exibidas no painel **Detalhes do dispositivo**:

![Exibir o novo dispositivo de mecanismo simulado](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

Em **detalhes do dispositivo**, verifique se o novo dispositivo está enviando telemetria. Para exibir os diferentes fluxos de telemetria do seu dispositivo, escolha um nome de telemetria como **vibração**:

![Selecionar um fluxo de telemetria a ser exibido](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

O painel **Detalhes do dispositivo** exibe outras informações sobre o dispositivo como valores de marca, os métodos com suporte e as propriedades relatadas pelo dispositivo.

Para exibir um diagnóstico detalhado, role para baixo até a exibição **Diagnóstico**.

## <a name="act-on-a-device"></a>Agir em um dispositivo

Para agir em um dispositivo, selecione-o na lista de dispositivos e, em seguida, escolha **Agendar**. O modelo de dispositivo **Mecanismo** especifica quatro métodos que precisam ter suporte em um dispositivo:

![Métodos de mecanismo](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

Escolha **Reiniciar**, defina o nome do trabalho **RestartEngine** e, em seguida, escolha **Aplicar**:

![Agendar o método reiniciar](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

Para acompanhar o status do trabalho na página **Manutenção**, escolha **Status do Sistema**:

![Monitorar o trabalho de agendas](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Métodos em outros dispositivos

Ao explorar os tipos de dispositivo simulado diferentes, você verá que outros tipos de dispositivo dão suporte a métodos diferentes. Em uma implantação com dispositivos físicos, o modelo de dispositivo especifica os métodos que precisam ter suporte no dispositivo. Normalmente, o desenvolvedor do dispositivo é responsável por desenvolver o código que faz com que o dispositivo aja em resposta a uma chamada de método.

Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista da página **Dispositivos**. O painel **Agenda** mostra os tipos de método comuns a todos os dispositivos selecionados.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para alterar a configuração de um dispositivo, selecione-o na lista de dispositivos da página **Dispositivos** e, em seguida, selecione **Reconfigurar**. O painel Reconfigurar mostra os valores de propriedade do dispositivo selecionado que podem ser alterados:

![Reconfigurar um dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

Para fazer uma alteração, adicione um nome para o trabalho, atualize os valores de propriedade e escolha **Aplicar**:

![Atualizar um valor da propriedade do dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

Para acompanhar o status do trabalho na página **Manutenção** escolha **Status do Sistema**.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Provisionar um dispositivo simulado.
> * Testar o dispositivo simulado.
> * Chamar os métodos de dispositivo por meio da solução.
> * Reconfigurar um dispositivo.

Agora que você aprendeu a gerenciar seus dispositivos, as próximas etapas sugeridas são aprender como:

* [Solucionar e corrigir problemas de dispositivo](iot-suite-remote-monitoring-maintain.md).
* [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).
* [Conectar o dispositivo à solução de monitoramento remoto pré-configurada](iot-suite-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->