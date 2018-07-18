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
ms.openlocfilehash: 5702c6e9c9d75c6cccb82f1c57684ef7b9898c34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665988"
---
## <a name="view-device-telemetry"></a>Exibir telemetria de dispositivo

Você pode exibir a telemetria enviada do dispositivo na página **Dispositivos** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos na página **Dispositivos**. Um painel exibe informações sobre seu dispositivo, incluindo um gráfico de telemetria do dispositivo:

    ![Consultar detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **Pressão** para alterar a exibição de telemetria:

    ![Exibir telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para exibir informações de diagnóstico sobre o dispositivo, role para baixo até **Diagnóstico**:

    ![Exibir diagnóstico do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir no dispositivo

Para invocar métodos em seus dispositivos, use a página **Dispositivos** na solução de Monitoramento Remoto. Por exemplo, na solução de Monitoramento Remoto, os dispositivos **Chiller** implementam um método **FirmwareUpdate**.

1. Escolha **Dispositivos** para navegar até a página **Dispositivos** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos da página **Dispositivos**:

    ![Selecionar o dispositivo físico](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para exibir uma lista dos métodos que você pode chamar em seu dispositivo, escolha **Trabalhos**, em seguida, **Executar método**. Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista. O painel **Trabalhos** mostra os tipos de método comuns a todos os dispositivos selecionados.

1. Escolha **FirmwareUpdate**, defina o nome do trabalho para **UpdatePhysicalChiller**. Defina a **Versão do Firmware** para **2.0.0**, defina **URI do Firmware** para **http://contoso.com/updates/firmware.bin**e, em seguida, escolha **Aplicar**:

    ![Agendar a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens é exibida no console executando o código de dispositivo, enquanto o dispositivo simulado manipula o método.

1. Quando a atualização estiver concluída, a nova versão do firmware é exibida na página **Dispositivos**:

    ![Atualização concluída](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Para acompanhar o status do trabalho na solução, escolha **Exibir**.

## <a name="next-steps"></a>Próximas etapas

O artigo [Personalizar o acelerador de Monitoramento Remoto pré-configurado](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descreve algumas maneiras de personalizar o acelerador da solução.