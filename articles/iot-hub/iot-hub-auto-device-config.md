---
title: Configurar e monitorar dispositivos IoT em escala com o Azure IoT Hub | Microsoft Docs
description: Usar configurações de dispositivo automático do Azure IoT Hub para atribuir uma configuração a vários dispositivos
services: iot-hub
documentationcenter: ''
author: ChrisGMsft
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 7146fba69857c3a612ce1b3dbb83387c1f3068d6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Configurar e monitorar dispositivos IoT em escala - versão prévia

O gerenciamento automático do dispositivo no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Com o gerenciamento automático do dispositivo, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que entrem no escopo.  Isso é realizado usando uma configuração automática do dispositivo, que também permitirá que você resuma a conclusão e conformidade, trate da mesclagem e conflitos e implante configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

As configurações automáticas do dispositivo trabalham ao atualizar um conjunto de dispositivos gêmeos com as propriedades desejadas e informam um resumo com base em propriedades reportadas de dispositivos gêmeos.  Ele apresenta uma nova classe e documento JSON chamado uma _Configuração_ que tem três partes:

* A **condição de destino** define o escopo de dispositivos gêmeos a serem atualizados. A condição de destino é especificada como uma consulta em marcas de dispositivos gêmeos e/ou propriedades reportadas.

* O **conteúdo de destino** define as propriedades desejadas para serem adicionadas ou atualizadas nos dispositivos gêmeos de destino. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. Métricas personalizadas são especificadas como consultas nas propriedades reportados do dispositivo gêmeo.  As métricas do sistema são métricas padrão que medem o status de atualização de dispositivos gêmeos, como o número de dispositivos gêmeos que são definidos como destino e o número de dispositivos gêmeos que foram atualizados com êxito. 

## <a name="implement-device-twins-to-configure-devices"></a>Implementar dispositivos gêmeos para configurar dispositivos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Consulte [Entender e usas dispositivos gêmeos no Hub IoT][lnk-device-twin] para orientação sobre como usar dispositivos gêmeos.

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma configuração, você deve especificar quais dispositivos você deseja afetar. O Azure IoT Hub identifica os dispositivos usando marcas no dispositivo gêmeo. Cada dispositivo pode ter várias marcações e você pode defini-las que qualquer modo que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em locais diferentes, poderá adicionar as seguintes marcas a um dispositivo gêmeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Criar uma configuração

1. No [Portal do Azure][lnk-portal], vá ao hub IoT. 
1. Selecione **Configuração do dispositivo (versão prévia)**.
1. Selecione **Adicionar configuração**.

Há cinco etapas para criar uma configuração. As seções a seguir explicam cada uma delas. 

### <a name="step-1-name-and-label"></a>Etapa 1: Nome e rótulo

1. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Adicione rótulos para ajudar a acompanhar as configurações. Os rótulos são pares de **Nome** e **Valor** que descrevem a configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.
1. Selecione **Avançar** para ir para a etapa 2. 

### <a name="step-2-specify-settings"></a>Etapa 2: Especificar as configurações

Esta seção especifica o conteúdo de destino a ser definido nos dispositivos gêmeos de destino. Há duas entradas para cada conjunto de configurações. A primeira é o caminho do dispositivo gêmeo, que é o caminho para a seção JSON dentro das propriedades desejadas do dispositivo gêmeo que será definido.  O segundo é o conteúdo do JSON a ser inserido nessa seção. Por exemplo, defina o caminho do dispositivo gêmeo e o conteúdo para o seguinte:

![Definir o caminho do dispositivo gêmeo e conteúdo](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Você também pode definir configurações individuais, especificando o caminho inteiro no caminho do dispositivo gêmeo e o valor do conteúdo sem colchetes. Por exemplo, defina o caminho do dispositivo gêmeo para `properties.desired.chiller-water.temperature` e defina o conteúdo para: `66`

Se duas ou mais configurações direcionam o mesmo caminho do dispositivo gêmeo, o conteúdo da configuração de prioridade mais alta será aplicado (a prioridade é definida na etapa 4).

Se você quiser remover uma propriedade, especifique o valor da propriedade como `null`.

Você pode adicionar configurações adicionais selecionando **Adicionar configuração do dispositivo gêmeo**

### <a name="step-3-specify-metrics-optional"></a>Etapa 3: Especificar as métricas (opcional)

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas.

1. Insira um nome para **Nome da métrica**
1. Insira uma consulta para **Critérios da métrica**.  A consulta é baseada nas propriedades reportadas de dispositivo gêmeo.  A métrica representa o número de linhas retornadas pela consulta.

Por exemplo: `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Você pode incluir uma cláusula que a configuração foi aplicada, por exemplo: `SELECT deviceId FROM devices WHERE configurations.yourconfigname.status='Applied'`


### <a name="step-4-target-devices"></a>Etapa 4: Direcionar dispositivos

Use a propriedade marcas dos dispositivos gêmeos para direcionar os dispositivos específicos que devem receber essa configuração.  Você também pode direcionar dispositivos por propriedades reportadas de dispositivo gêmeo.

Como várias configurações podem direcionar o mesmo dispositivo, você deve atribuir a cada configuração um número de prioridade. Se houver um conflito, a configuração com a prioridade mais alta ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da configuração. O maior valor numérico é considerado a prioridade mais alta. Se duas configurações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa configuração. A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. 
1. Selecione **Avançar** para ir para a etapa final.

### <a name="step-5-review-configuration"></a>Etapa 5: Revisar configuração

Revise as informações da configuração e, em seguida, selecione **Enviar**.

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Para exibir os detalhes de uma configuração e monitorar os dispositivos que a executam, use as seguintes etapas:

1. No [Portal do Azure][lnk-portal], vá ao hub IoT. 
1. Selecione **Configuração do dispositivo (versão prévia)**.
1. Inspecione a lista de configuração. Para cada configuração, você pode exibir os seguintes detalhes:
   * **ID** - o nome da configuração.
   * **Condição de destino** - a consulta usada para definir os dispositivos direcionados.
   * **Prioridade** - o número de prioridade atribuído à configuração.
   * **Hora de criação** - o carimbo de data/hora de quando a configuração foi criada. Esse carimbo de data/hora é usado para desempate quando duas configurações têm a mesma prioridade. 
   * **As métricas do sistema** - métricas que são calculadas pelo Hub IoT e não podem ser personalizadas pelos desenvolvedores. O destino especifica o número de dispositivos gêmeos que correspondem à condição de destino. Aplica-se o número especificado de dispositivos gêmeos que foram modificados pela configuração, que pode incluir modificações parciais no evento que uma configuração separada de prioridade mais alta também fez alterações. 
   * **Métricas personalizadas** -métricas que foram especificadas pelo desenvolvedor como consultas em propriedades reportadas de dispositivo gêmeo.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
1. Selecione a configuração que deseja monitorar.  
1. Inspecione os detalhes da configuração. Use guias para exibir detalhes específicos sobre os dispositivos que receberam a configuração: 
   * **Condição de destino** - os dispositivos que correspondem à condição de destino. 
   * **Métricas** - uma lista de métricas do sistema e métricas personalizadas.  Você pode exibir uma lista de dispositivos que são contadas para cada métrica selecionando a métrica na lista suspensa e, em seguida, selecionando **Exibir dispositivos**.
   * **Configurações do dispositivo gêmeo** - as configurações de dispositivo gêmeo que são definidas pela configuração. 
   * **Rótulos de configuração** - pares de chave-valor usados para descrever uma configuração.  Rótulos não têm impacto sobre a funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:
* Caso um dispositivo gêmeo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa configuração tiver a prioridade mais alta para o dispositivo gêmeo, essa configuração será aplicada ao dispositivo gêmeo. 
* Se um dispositivo gêmeo não atende mais à condição de destino, as definições da configuração serão removidas e o dispositivo gêmeo será modificada pela próxima configuração de prioridade mais alta. 
* Se um dispositivo gêmeo que executa essa configuração no momento não atende à condição de destino e não atende a condição destino de quaisquer outras configurações, as definições da configuração serão removidas e nenhuma outra alteração será feita no dispositivo gêmeo. 

Para modificar uma configuração, use as seguintes etapas: 

1. No [Portal do Azure][lnk-portal], vá ao hub IoT. 
1. Selecione **Configuração do dispositivo (versão prévia)**. 
1. Selecione a configuração que deseja modificar. 
1. Faça atualizações nos seguintes campos: 
   * Condição de destino 
   * Rótulos 
   * Prioridade 
   * Métricas
1. Clique em **Salvar**.
1. Siga as etapas em [Monitorar uma implantação][âncora-monitor] para observar as alterações serem distribuídas. 

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeo assume sua próxima configuração de prioridade mais alta. Se os dispositivos gêmeos não atendem a condição destino de qualquer outra configuração, não serão aplicadas outras configurações. 

1. No [Portal do Azure][lnk-portal], vá ao hub IoT. 
1. Selecione **Configuração do dispositivo (versão prévia)**. 
1. Use a caixa de seleção para selecionar a configuração que deseja excluir. 
1. Selecione **Excluir**.
1. Uma mensagem solicitará que você confirme.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar e monitorar dispositivos IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciar identidades de dispositivo do Hub IoT em massa][lnk-bulkIDs]
* [Métricas do Hub IoT][lnk-metrics]
* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Implantação do IA em dispositivos de borda com o Azure IoT Edge][lnk-iotedge]

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
