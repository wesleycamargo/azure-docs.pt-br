---
title: Como as instruções para modificar um ASC para IoT gêmeo de módulo no ASC para versão prévia do IoT | Microsoft Docs
description: Saiba como modificar um ASC para gêmeo do módulo de segurança de IoT para ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541941"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Modificar um ASC para gêmeo de módulo do IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como modificar a configuração de existente **AzureIoTSecurity módulo gêmeo** para um dispositivo existente. 

Ver [criar um ASC para o módulo do IoT](quickstart-create-security-twin.md) para aprender a fazer um novo módulo de segurança para um novo dispositivo.  

## <a name="modification-considerations"></a>Considerações de modificação

> [!IMPORTANT]
> Cada configuração na configuração do gêmeo substitui a configuração padrão. Se uma configuração específica não está mais presente na configuração do gêmeo, a configuração padrão é usada. 

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação 

Certifique-se de validar a configuração do agente em relação a este [esquema](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Um agente não serão iniciados se o objeto de configuração não coincide com o esquema.

 
Se, enquanto o agente é executado, o objeto de configuração é alterado para uma configuração inválida (a configuração não coincide com o esquema), o agente irá ignorar a configuração inválida e continuarão a usar a configuração atual. 

## <a name="edit-a-property"></a>Editar uma propriedade  

Defina todas as propriedades personalizadas dentro do objeto de configuração do agente dentro do gêmeo do módulo AzureIoTSecurity. 

Para definir uma propriedade, adicione a chave de propriedade para o objeto de configuração com o valor desejado. Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>propriedades 
A tabela a seguir contém todas as propriedades configuráveis que controlam o ASC para agentes de IoT. 
          

| NOME| Status | Valores válidos| Valores padrão| DESCRIÇÃO |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obrigatório: false |Valores válidos:  Duração em formato ISO 8601 |Valor padrão: PT7M |Tempo máximo antes das mensagens de alta prioridade são enviados.|
|lowPriorityMessageFrequency |Obrigatório: false|Valores válidos:  Duração em formato ISO 8601 |Valor padrão: PT5H |Tempo máximo antes das mensagens de baixa prioridade são enviados.| 
|snapshotFrequency |Exigir: false|Valores: duração válido no formato ISO 8601 |Valor padrão PT13H |Intervalo de tempo para a criação de instantâneos de status do dispositivo.| 
|maxLocalCacheSizeInBytes |Obrigatório: false |Valores válidos:  |Valor padrão: 2560000, maior que 8192 | Armazenamento máximo (em bytes) permitido para o cache de mensagens de um agente. Quantidade máxima de espaço permitido para armazenar mensagens do dispositivo antes que as mensagens são enviadas.| 
|maxMessageSizeInBytes |Obrigatório: false |Valores válidos:  Um número positivo maior que 8192, menos 262144 |Valor padrão: 204800 |Máximo tamanho permitido de um agente de mensagem de nuvem. Essa configuração controla a quantidade máxima de dados enviada em cada mensagem. |
|eventPriority${EventName} |Obrigatório: false |Valores válidos:  Alta, baixa, desativado |Valores padrão: |Prioridade de cada agente gerou o evento. | 
|

### <a name="events"></a>Eventos

A lista de eventos a seguir são todos os eventos a ASC para agente do IoT podem coletar de seus dispositivos. Use o gêmeo do módulo AzureIotSecurity para configurar quais esses eventos são coletados e decidir sua prioridade em sua solução. 
 
|Nome do evento| PropertyName | Valor padrão| Evento de instantâneo| Status detalhado  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventPriorityDiagnostic| Desativar| Falso| Eventos de diagnóstico relacionadas ao agente. Use esse evento para o log detalhado.| 
Erro de configuração |eventPriorityConfigurationError |Baixo |Falso |Falha ao analisar a configuração do agente. Verifique a configuração em relação ao esquema.| 
|Eventos ignorados estatísticas |eventPriorityDroppedEventsStatistics |Baixo |True|Estatísticas de eventos relacionados ao agente. |
|Estatísticas de mensagem|eventPriorityMessageStatistics |Baixo |True |Estatísticas de mensagens relacionadas ao agente. |
|Hardware conectado|eventPriorityConnectedHardware |Baixo |True |Instantâneo de todos os hardwares conectados ao dispositivo.|
|Portas de escuta|eventPriorityListeningPorts |Alto |True |Instantâneo de todas as portas de escuta no dispositivo.|
| Criar processo |eventPriorityProcessCreate |Baixo |Falso |Auditorias de processam de criação do dispositivo.|
| Encerrar processo|eventPriorityProcessTerminate |Baixo |Falso |Auditorias de processam o encerramento no dispositivo.| 
 Informações do sistema |eventPrioritySystemInformation |Baixo |True |Um instantâneo de informações do sistema, como CPU ou de sistema operacional.|
|Usuários locais| eventPriorityLocalUsers |Alto |True|Um instantâneo dos usuários registrados locais dentro do sistema. |
|Logon|  eventPriorityLogin |Alto|Falso|Audita os eventos de logon para o dispositivo (logons locais e remotos).|
|Criar Conexão |eventPriorityConnectionCreate|Baixo|Falso|Audita as conexões TCP criadas para e do dispositivo. |
|Configuração do firewall| eventPriorityFirewallConfiguration|Baixo|True|Instantâneo da configuração de firewall de dispositivo (regras de firewall). |
|Linha de base do sistema operacional| eventPriorityOSBaseline| Baixo|True|Verifique se o instantâneo de linha de base do dispositivo do sistema operacional.| 
|


## <a name="next-steps"></a>Próximas etapas

- Leia o ASC para IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Compreender e explorar [ASC para alertas de IoT](concept-security-alerts.md)
- Descubra como acessar sua [dados de segurança](how-to-security-data-access.md)
