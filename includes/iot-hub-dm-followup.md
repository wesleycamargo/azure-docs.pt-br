---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 3cdfa74c5f124e57b125399e39b7981b1dfb6d09
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43100226"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e estender as ações de gerenciamento do dispositivo

As soluções de IoT podem expandir o conjunto definido de padrões de gerenciamento do dispositivo ou habilitar padrões personalizados usando o dispositivo gêmeo e os primitivos do método da nuvem para o dispositivo. Outros exemplos de ações de gerenciamento do dispositivo incluem a redefinição de fábrica, atualização do firmware, atualização do software, gerenciamento de energia, gerenciamento da rede e da conectividade, e criptografia dos dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo

Normalmente, você pode configurar os dispositivos para executar ações em um horário que minimiza as interrupções e a inatividade. As janelas de manutenção do dispositivo são um padrão usado para definir a hora em que um dispositivo deve atualizar sua configuração. As soluções de back-end podem usar as propriedades desejadas do dispositivo gêmeo para definir e ativar uma política no dispositivo que permite uma janela de manutenção. Quando um dispositivo recebe a política da janela de manutenção, ele pode usar a propriedade relatada do dispositivo gêmeo para informar o status da política. O aplicativo de back-end pode usar as consultas do dispositivo gêmeo para atestar a conformidade dos dispositivos e cada política.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um método direto para disparar uma reinicialização remota em um dispositivo. Você usou as propriedades relatadas para relatar a hora da última reinicialização do dispositivo e consultou o dispositivo gêmeo para descobrir a hora da última reinicialização do dispositivo na nuvem.

Para continuar com a introdução ao Hub IoT e aos padrões de gerenciamento de dispositivo como remoto por meio da atualização de firmware de ar, consulte:

[Tutorial: Como realizar uma atualização de firmware](../articles/iot-hub/tutorial-firmware-update.md)

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, veja o tutorial [Agendar e difundir trabalhos](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).

Para continuar a introdução ao Hub IoT, veja [Introdução ao IoT Edge](../articles/iot-edge/tutorial-simulate-device-linux.md).