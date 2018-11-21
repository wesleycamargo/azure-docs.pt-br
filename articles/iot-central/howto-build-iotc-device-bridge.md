---
title: Compilar a ponte de dispositivo do Azure IoT Central | Microsoft Docs
description: Compile a ponte de dispositivo do IoT Central para conectar outras nuvens de IoT (Sigfox, Particle, The Things Network etc.) ao aplicativo IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/8/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 83f053a8815f31803f536920497fdc42e72d2a2d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628261"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Compilar a ponte de dispositivo do IoT Central para conectar outras nuvens de IoT ao IoT Central

*Este tópico aplica-se aos administradores.*

A ponte de dispositivo do IoT Central é uma solução de software livre que conecta o Sigfox, Particle, The Things Network e outras nuvens ao aplicativo IoT Central. Se você estiver usando dispositivos de rastreamento de ativos conectados à Rede de Longa Distância de baixo consumo de energia da Sigfox, usando dispositivos de monitoramento da qualidade do ar no Particle Device Cloud ou, usando dispositivos de monitoramento de umidade do solo no TTN, você poderá aproveitar diretamente a potência do IoT Central usando a ponte de dispositivo do IoT Central. A ponte de dispositivo conecta outras nuvens de IoT com o IoT Central, enviando os dados que os dispositivos enviam para outras nuvens até o aplicativo IoT Central. No aplicativo IoT Central, é possível compilar regras e executar análises nesses dados, criar fluxos de trabalho no Microsoft Flow e Aplicativos Lógicos do Azure, exportar esses dados, e muito mais. Obter a [ponte de dispositivo do IoT Central](https://aka.ms/iotcentralgithubdevicebridge) do Github

## <a name="what-is-it-and-how-does-it-work"></a>O que é e como funciona?
A ponte de dispositivo do IoT Central é uma solução de software livre no Github. Ela está pronta para ser usada com o botão "Implantar no Azure", que implanta um modelo personalizado do Azure Resource Manager com vários recursos do Azure na sua assinatura do Azure. Os recursos incluem:
-   Aplicativo Azure Functions
-   Conta de Armazenamento do Azure
-   Plano de serviço de aplicativo (camada S1)
-   Azure Key Vault. O aplicativo de funções é a parte crítica da ponte de dispositivo. Ele recebe solicitações HTTP POST de outras plataformas de IoT ou de qualquer plataforma personalizada por meio de uma integração de webhook simples. Nós fornecemos exemplos que mostram como conectar as nuvens Sigfox, Particle e TTN. É possível estender facilmente essa solução para conectar-se à sua nuvem de IoT personalizada, caso sua plataforma possa enviar solicitações de HTTP POST ao aplicativo de funções.
O aplicativo de funções transforma os dados em um formato aceito pelo IoT Central e os encaminha via APIs de DPS.

![Captura de tela do Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Se o aplicativo IoT Central reconhecer o dispositivo por ID de dispositivo na mensagem encaminhada, uma nova medida será exibida para esse dispositivo. Se a ID do dispositivo nunca for verificada pelo aplicativo IoT Central, o aplicativo de funções tentará registrar um novo dispositivo com essa ID do dispositivo e ele aparecerá como um "Dispositivo não associado" no aplicativo IoT Central. 

## <a name="how-do-i-set-it-up"></a>Como fazer para configurar?
As instruções estão listadas em detalhes no arquivo LEIAME no repositório do Github. 

## <a name="pricing"></a>Preços
Isso tudo está hospedado na sua assinatura do Azure. A maior parte dos custos estimados dos recursos provisionados é proveniente do [preço de um Plano de Serviço de Aplicativo padrão]( https://azure.microsoft.com/en-us/pricing/details/app-service/windows/). Você pode saber mais sobre isso e as possíveis maneiras de reduzi-los no arquivo LEIAME.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como compilar a ponte de dispositivo do IoT Central, a seguir está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Gerenciar seus dispositivos](howto-manage-devices.md)