---
title: Diagnosticar e solucionar problemas de desconexões com o Hub do Azure IoT
description: Aprenda a diagnosticar e solucionar erros comuns com a conectividade de dispositivos do Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: eb186f4b6e1d742c9cae51e68b3d3dbda1bb751c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400149"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Detectar e solucionar problemas de desconexões com o Hub do Azure IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos possíveis pontos de falha. A lógica do aplicativo do dispositivo, as redes físicas, os protocolos, o hardware e o Azure IoT Hub podem causar problemas. Este documento fornece recomendações sobre como detectar e solucionar problemas de conectividade de dispositivos do lado da nuvem (em oposição ao lado do dispositivo).

## <a name="get-alerts-and-error-logs"></a>Obtenha alertas e logs de erros

Use o Azure Monitor para obter alertas e gravar logs quando as conexões de dispositivo solta.

### <a name="turn-on-diagnostic-logs"></a>Ativar os Logs de diagnóstico 

Para registrar erros e eventos de conexão do dispositivo, ative o diagnóstico do Hub IoT. 

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até o seu Hub IoT.
1. Selecionar **configurações de Diagnóstico**.
1. Em seguida, selecione **ativar o diagnóstico**.
1. Verifique se você habilitou **conexões** logs a serem coletados. 
1. Para facilitar a análise, você deve ativar **enviar para Log Analytics** ([consulte preços](https://azure.microsoft.com/pricing/details/log-analytics/)). Um exemplo posteriormente neste artigo usa o Log Analytics.

   ![Configurações recomendadas][2]

Para obter mais informações, consulte [monitorar a integridade do IoT Hub do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Configurar alertas para a métrica de contagem de dispositivos conectados

Para receber alertas quando os dispositivos são desconectados, configure alertas na métrica *Dispositivos conectados*. 

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até seu Hub IoT.
1. Selecione **alertas (clássico)**.
1. Clique em **adicionar alerta de métrica (clássico)**.
1. Preencha o formulário e selecione **Ok**. 

   ![Alerta de métrica recomendada][3]

Para saber mais, consulte [O que são alertas clássicos no Microsoft Azure?](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="resolve-common-connectivity-errors"></a>Resolver erros comuns de conectividade

Quando os registros e alertas de diagnóstico dos dispositivos conectados estão ativados, você recebe alertas quando as coisas dão errado. Esta seção descreve como resolver problemas comuns quando você recebe um alerta. As etapas abaixo supõem que você tenha configurado o Log Analytics para seus registros de diagnóstico. 

1. Acesse seu espaço de trabalho **do Log Analytics** no portal do Azure.
1. Clique em **pesquisa de Log**.
1. Para isolar os logs de erros de conectividade para o IoT Hub, insira esta consulta na caixa, pressione **executar**.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure o `OperationName`, `ResultType` (código de erro), e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de log de erros][4]

1. Use esta tabela para entender e resolver erros comuns.

    | Erro | Causa raiz | Resolução |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | DeviceConnectionClosedRemotely 404104 | A conexão foi fechada pelo dispositivo, mas o IoT Hub não sabe por quê. Causas comuns incluem tempo limite de MQTT / AMQP e perda de conectividade com a Internet. | Certifique-se de que o dispositivo possa se conectar ao Hub IoT até [testar a conexão](tutorial-connectivity.md). Se a conexão estiver boa, mas o dispositivo desconectar de forma intermitente, certifique-se de implementar a lógica adequada do dispositivo keep alive para sua escolha de protocolo (MQTT / AMPQ). |
    | IoTHubUnauthorized 401003 | O IoT Hub não foi possível autenticar a conexão. | Certifique-se de que o SAS ou outro token de segurança usado não esteja expirado. Os [SDKs IoT do Azure](iot-hub-devguide-sdks.md) geram automaticamente tokens sem exigir configuração especial. |
    | LinkCreationConflict 409002 | Existem mais de uma conexão para o mesmo dispositivo. Quando uma nova solicitação de conexão chega para um dispositivo, o Hub IoT fecha a anterior com esse erro. | Certifique-se de emitir uma nova solicitação de conexão somente se a conexão cair. |
    | ServerError 500001 | O Hub IoT encontrou um problema no lado do servidor. Muito provavelmente, o problema é transitório. Embora a equipe do Hub IoT trabalhe arduamente para manter [o SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), pequenos subconjuntos de nós do Hub IoT podem ocasionalmente sofrer falhas transitórias. Quando o dispositivo tenta se conectar a um nó com problemas, você recebe esse erro. | Para atenuar a falha transitória, emita uma nova tentativa do dispositivo. Para [gerenciar automaticamente as novas tentativas](iot-hub-reliability-features-in-sdks.md), certifique-se de usar a versão mais recente dos [SDKs do Azure IoT](iot-hub-devguide-sdks.md).<br><br>Como prática recomendada no tratamento de falhas transitórias e novas tentativas, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults.md).  <br><br>Se o problema persistir após várias tentativas, verifique [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) e [Status do Azure](https://azure.microsoft.com/status/history/) para ver se o IoT Hub tem um problema conhecido. Se não há nenhum problema conhecido e o problema persistir, [entre em contato com o suporte](https://azure.microsoft.com/support/options/) para uma investigação adicional. |
    | GenericTimeout 500008 | O Hub IoT não pôde concluir a solicitação de conexão antes do tempo limite. Como 500001 ServerError, esse erro provavelmente é transitório. | Siga as etapas de solução de problemas para 500001 ServerError para a causa raiz e resolva esse erro.|

## <a name="other-steps-to-try"></a>Outras etapas para experimentar

Se as etapas acima não ajudarem, veja mais algumas coisas para experimentar:

* Se você tiver acesso aos dispositivos problemáticos, seja física ou remotamente (como o SSH), siga o [guia de solução de problemas do lado do dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar com a solução de problemas.
* Verifique se seus dispositivos são **Habilitados** no portal do Azure> seu Hub IoT> dispositivos de IoT.
* Obter ajuda sobre [Fórum do IoT Hub do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), ou [suporte do Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário abaixo se este guia não o ajudar.

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
