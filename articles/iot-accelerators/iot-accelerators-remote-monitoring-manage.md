---
title: Gerenciar dispositivos em uma solução de monitoramento remoto baseada no Azure | Microsoft Docs
description: Este tutorial mostra como gerenciar dispositivos conectados ao acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: cd8e8c1fe1b77113968b7af635f45f9e0e077b7c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159190"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Tutorial: Configurar e gerenciar dispositivos conectados à sua solução de monitoramento

Neste tutorial, você usará o acelerador da solução de Monitoramento Remoto para identificar e corrigir problemas em dispositivos IoT conectados. Adicione um novo dispositivo ao acelerador de solução, configure o dispositivo e atualize o firmware do dispositivo.

A Contoso solicitou novas máquinas para expandir uma de suas instalações. Enquanto espera a entrega das novas máquinas, você pode executar uma simulação para testar o comportamento da solução. Para executar a simulação, você pode adicionar um novo dispositivo de mecanismo simulado ao acelerador de solução de Monitoramento Remoto e testar se esse dispositivo simulado responde corretamente a ações e atualizações de configuração.

Para fornecer uma maneira extensível de configurar e gerenciar dispositivos, o acelerador de solução de Monitoramento Remoto usa recursos do Hub IoT como [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) e [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md). Embora este tutorial use dispositivos simulados, um desenvolvedor de dispositivo pode implementar métodos diretos em um [dispositivo físico conectado ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).

Neste tutorial, você irá:

>[!div class="checklist"]
> * Provisionar um dispositivo simulado.
> * Testar um dispositivo simulado.
> * Atualizar o firmware de um dispositivo.
> * Reconfigurar um dispositivo.
> * Organizar seus dispositivos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Navegue até a página **Dispositivos** na solução e clique em **+ Novo dispositivo**:

[![Provisionar um dispositivo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

No painel **Novo dispositivo**, escolha **Simulado**, deixe o número de dispositivos a serem provisionados como **1**, escolha o modelo de dispositivo **Mecanismo Defeituoso** e escolha **Aplicar** para criar o dispositivo simulado:

[![Provisionar um dispositivo de mecanismo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para testar se o dispositivo de mecanismo simulado está enviando a telemetria e relatando valores de propriedade, selecione-o na lista de dispositivos na página **Dispositivos**. Informações em tempo real sobre o mecanismo são exibidas no painel **Detalhes do Dispositivo**:

[![Exibir o novo dispositivo de mecanismo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

Em **Detalhes do Dispositivo**, verifique se o novo dispositivo está enviando telemetria. Para exibir o fluxo de telemetria de vibração do dispositivo, clique em **Vibração**:

[![Selecionar um fluxo de telemetria a ser exibido](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

O painel **Detalhes do Dispositivo** exibe outras informações sobre o dispositivo como valores de marca, os métodos com suporte e as propriedades relatadas pelo dispositivo.

Para exibir um diagnóstico detalhado, role para baixo no painel **Detalhes do dispositivo** para exibir a seção **Diagnóstico**.

## <a name="act-on-a-device"></a>Agir em um dispositivo

Para testar se o dispositivo de mecanismo simulado responde corretamente às ações iniciadas no painel, execute o método **FirmwareUpdate**. Para atuar em um dispositivo com a execução de um método, selecione o dispositivo na lista de dispositivos e clique em **Trabalhos**. Você poderá selecionar mais de um dispositivo se quiser agir em vários dispositivos. No painel **Trabalhos**, selecione **Executar método**. O modelo de dispositivo **Mecanismo** especifica três métodos: **FirmwareUpdate**, **FillTank** e **EmptyTank**:

[![Métodos de mecanismo](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Escolha **FirmwareUpdate**, defina o nome do trabalho como **UpdateEngineFirmware**, defina a versão do firmware como **2.0.0**, defina o URI do firmware como **http://contoso.com/engine.bin**e clique em **Aplicar**:

[![Agendar o método de atualização do firmware](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Para acompanhar o status do trabalho, clique em **Exibir status do trabalho**:

[![Monitorar o trabalho de atualização de firmware agendado](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Depois que o trabalho for concluído, navegue de volta para a página **Dispositivos**. A nova versão do firmware é exibida para o dispositivo de mecanismo.

Se você selecionar vários dispositivos de tipos diferentes na página **Dispositivos**, ainda poderá criar um trabalho para executar um método nesses vários dispositivos. O painel **Trabalhos** mostra somente os tipos de método comuns a todos os dispositivos selecionados.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para testar se você pode atualizar as propriedades de configuração do mecanismo, selecione-o na lista de dispositivos na página **Dispositivos**. Em seguida, clique em **Trabalhos**e escolha **Reconfigurar**. O painel Trabalhos mostra os valores de propriedade do dispositivo selecionado que podem ser alterados:

[![Reconfigurar um dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Para atualizar o local do mecanismo, defina o nome do trabalho como **UpdateEngineLocation**, defina a longitude como **-122.15**, defina o local como **Factory 2**, defina a latitude como **47.62** e clique em **Aplicar**:

[![Atualizar um valor da propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Para acompanhar o status do trabalho, clique em **Exibir status do trabalho**:

[![Atualizar um valor da propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Depois que o trabalho for concluído, navegue até a página **Painel**. O dispositivo de mecanismo é exibido no mapa em seu novo local:

[![Exibir local do mecanismo](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizar os dispositivos

Para facilitar seu trabalho como operador de organizar e gerenciar dispositivos, você pode marcá-los com o nome da equipe. A Contoso tem duas equipes diferentes para atividades de serviço de campo:

* A equipe Veículo Inteligente gerencia caminhões e dispositivos de protótipo.
* A equipe Prédio Inteligente gerencia resfriadores, elevadores e mecanismos.

Para exibir seus dispositivos, navegue até a página **Dispositivos** e selecione o filtro **Todos os dispositivos**:

[![Mostrar todos os dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Adicionar marcas

Selecione todos os dispositivos **Caminhões** e **Protótipos**. Em seguida, clique em **Trabalhos**.

No painel **Trabalhos**, selecione **Marca**, defina o nome do trabalho como **AddConnectedVehicleTag** adicione uma marca de texto chamada **FieldService** com um valor **ConnectedVehicle**. Em seguida, clique em **Aplicar**:

[![Adicionar marcas aos dispositivos de protótipo e caminhão](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na página do dispositivo, selecione todos os dispositivos **Resfriador**, **Elevador**, e **Mecanismo**. Em seguida, clique em **Trabalhos**.

No painel **Trabalhos**, selecione **Marca**, defina o nome do trabalho como **AddSmartBuildingTag** e adicione uma marca de texto chamada **FieldService** com um valor **SmartBuilding**. Em seguida, clique em **Aplicar**:

[![Adicionar marca aos dispositivos Resfriador, Elevador e Mecanismo](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Criar filtros

Agora você pode usar os valores da marca para criar filtros. Na página **Dispositivos**, clique em **Gerenciar grupos de dispositivos**:

[![Gerenciar grupos de dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Crie um filtro de texto que use o nome de marca **FieldService** e o valor **SmartBuilding** na condição. Salve o filtro como **Prédio Inteligente**:

[![Criar um filtro de prédio inteligente](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Crie um filtro de texto que use o nome de marca **FieldService** e o valor **ConnectedVehicle** na condição. Salve o filtro como **Veículo Conectado**.

[![Criar um filtro de veículo conectado](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Agora, o operador da Contoso pode consultar dispositivos de acordo com a equipe operacional:

[![Criar um filtro de veículo conectado](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como configurar e gerenciar os dispositivos conectados para o acelerador de solução de Monitoramento Remoto. Para saber como usar o acelerador de solução para identificar e corrigir problemas com seus dispositivos conectados, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Usar alertas do dispositivo para identificar e corrigir problemas em dispositivos conectados à solução de monitoramento](iot-accelerators-remote-monitoring-maintain.md)
